# Overview #

This tutorial will explain how to use the C++ Swarm client library for communication.

We assume that you have already done the [installation step](https://bitbucket.org/osrf/swarm/wiki/Install) and you know [how to write your own custom controller](https://bitbucket.org/osrf/swarm/wiki/Tutorial_1-How_to_create_your_Swarm_controller).

# The communication API explained ###

The Swarm client library contains three functions related with communications:

* **Bind(callback, address, port)**: This method binds an address and port to a virtual socket, and sends incoming messages to the specified callback.
* **SendTo(data, address, port)**: This method allows an agent to send data to other individual agent (unicast), all the agents (broadcast), or a group of agents (multicast). The addresses for sending broadcast and multicast messages are `kBroadcast` and `kMulticast` respectively.
* **Host()**: This method returns the address of the robot where the controller is running.
* **Neighbors()**: This method returns the addresses of other vehicles that are inside the communication range of this robot.

You can check the [C++ API](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/index.html) full documentation.

# Simple communication among agents #

1. First, create a `swarm_controller3` directory:

        mkdir swarm_controller3
        cd swarm_controller3

1. Download the example files into the `swarm_controller3` directory:

        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_3/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_3/TeamControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_3/TeamControllerPlugin.cc

1. Create a `build` directory:

        mkdir build
        cd build

1. Compile the controller:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

1. Execute the controller:

        gzserver worlds/ground_simple_2.world --verbose

You should see the following output:


```
#!python

Gazebo multi-robot simulator, version 6.0.8
Copyright (C) 2012-2015 Open Source Robotics Foundation.
Released under the Apache 2 License.
http://gazebosim.org

[Msg] Waiting for master.
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Publicized address: 172.23.1.7
[Msg] BrokerPlugin::ReadSwarmFromSDF: 2 swarm members found
[Msg] [192.168.2.1] Neighbors:
[Msg] 	192.168.2.2
[Msg] [192.168.2.2] Neighbors:
[Msg] 	192.168.2.1
[Msg] ---
[Msg] [192.168.2.2] New message received
[Msg] 	From: [192.168.2.1]
[Msg] 	Data: [Unicast data]
[Msg] ---
[Msg] [192.168.2.1] New message received
[Msg] 	From: [192.168.2.1]
[Msg] 	Data: [Broadcast data]
[Msg] ---
[Msg] [192.168.2.2] New message received
[Msg] 	From: [192.168.2.1]
[Msg] 	Data: [Broadcast data]
[Msg] ---
[Msg] [192.168.2.1] New message received
[Msg] 	From: [192.168.2.1]
[Msg] 	Data: [Multicast data]
[Msg] ---
[Msg] [192.168.2.2] New message received
[Msg] 	From: [192.168.2.1]
[Msg] 	Data: [Multicast data]
[Msg] ---
[Msg] [192.168.2.1] New message received
[Msg] 	From: [192.168.2.2]
[Msg] 	Data: [Unicast data]
[Msg] ---
[Msg] [192.168.2.1] New message received
[Msg] 	From: [192.168.2.2]
[Msg] 	Data: [Broadcast data]
[Msg] ---
[Msg] [192.168.2.2] New message received
[Msg] 	From: [192.168.2.2]
[Msg] 	Data: [Broadcast data]
[Msg] ---
[Msg] [192.168.2.1] New message received
[Msg] 	From: [192.168.2.2]
[Msg] 	Data: [Multicast data]
[Msg] ---
[Msg] [192.168.2.2] New message received
[Msg] 	From: [192.168.2.2]
[Msg] 	Data: [Multicast data]

```

# Walkthrough #

In this example, we are loading two vehicles with addresses `192.168.2.1` and `192.168.2.2`. If you open the world file distributed with your Swarm client library (`/usr/share/gazebo-6.0/worlds/ground_simple_2.world`), you will be able to see the model blocks and how the address and other parameters are specified:

```
#!xml

<!-- Robot #0-->
<model name="ground_0">
  
  [...]

  <!-- Load the plugin to control this robot -->
  <plugin name="swarm_controller_0" filename="libTeamControllerPlugin.so">
    [...]
    <address>192.168.2.1</address>
    <num_messages>1</num_messages>
  </plugin>
</model>

<!-- Robot #1-->
<model name="ground_1">

  [...]  

  <!-- Load the plugin to control this robot -->
  <plugin name="swarm_controller_1" filename="libTeamControllerPlugin.so">
    [...]
    <address>192.168.2.2</address>
    <num_messages>1</num_messages>
  </plugin>
</model>
```

Once a model loads its controller plugin, its `Load()` function is executed. This function executes only one time and it is the recommended place for reading SDF parameters and binding to your local address. Do not try to do these tasks in the class constructor because some of the Gazebo components might not be ready.

```
#!c++

//////////////////////////////////////////////////
void TeamControllerPlugin::Load(sdf::ElementPtr _sdf)
{
  // Read the <num_messages> SDF parameter.
  if (!_sdf->HasElement("num_messages"))
  {
    gzerr << "TeamControllerPlugin::Load(): Unable to find the <num_messages> "
          << "parameter" << std::endl;
    return;
  }

  this->numMessageToSend = _sdf->Get<int>("num_messages");

  // Bind on my local address and default port.
  this->Bind(&TeamControllerPlugin::OnDataReceived, this, this->Host());

  // Bind on the multicast group and default port.
  this->Bind(&TeamControllerPlugin::OnDataReceived, this, this->kMulticast);
}
```

As you can see in the previous snippet, we are using `HasElement()` and `Get()` functions for handling custom SDF parameters (in this case the parameter is `<num_messages>`. We also bind to our local and multicast addresses for receiving unicast and multicast messages. In this example we use the same callback but you could use different callbacks if you prefer to dispatch the messages differently. Note that the ports are optional and if not used, the default port will be used.


After executing the `Load()` function, each controller periodically executes its `TeamControllerPlugin::Update()` function. In the following snippet we can see how the different messages are sent:

```
#!c++

// Check if we already reached the limit of messages to be sent.
if (this->msgsSent < this->numMessageToSend)
{
  this->msgsSent++;

  std::string dstAddress;

  if (this->Host() == "192.168.2.1")
    dstAddress = "192.168.2.2";
  else
    dstAddress = "192.168.2.1";

  // Send a unicast message.
  if (!this->SendTo("Unicast data", dstAddress))
  {
    gzerr << "[" << this->Host() << "] TeamControllerPlugin::Update(): "
          << "Error sending a message to <" << dstAddress << ",DEFAULT_PORT>"
          << std::endl;
    return;
  }

  // Send a broadcast message.
  dstAddress = this->kBroadcast;
  if (!this->SendTo("Broadcast data", dstAddress))
  {
    gzerr << "[" << this->Host() << "] TeamControllerPlugin::Update(): "
          << "Error sending a message to <" << dstAddress << ",DEFAULT_PORT>"
          << std::endl;
    return;
  }

  // Send a multicast message.
  dstAddress = this->kMulticast;
  if (!this->SendTo("Multicast data", dstAddress))
  {
    gzerr << "[" << this->Host() << "] TeamControllerPlugin::Update(): "
          << "Error sending a message to <" << dstAddress << ",DEFAULT_PORT>"
          << std::endl;
    return;
  }
}
```

The first task that the controller does is to set the destination address. Then, the controller uses the function `SendTo()` for sending a unicast message, a broadcast message and a multicast message. Note that you don't need to `Bind()` for sending messages. `Bind()` is only used if you are interested on receiving messages. The next snippet shows how to print the list of local neighbors to each robot:

```
#!python

// Show the list of neighbors.
if (this->Neighbors().empty())
  gzmsg << "[" << this->Host() << "] Neighbors: EMPTY" << std::endl;
else
{
  gzmsg << "[" << this->Host() << "] Neighbors:" << std::endl;
  for (auto const &neighbor : this->Neighbors())
    gzmsg << "\t" << neighbor << std::endl;
}

```

Finally, the following snippet shows the callback associated to the incoming messages:


```
#!c++

//////////////////////////////////////////////////
void TeamControllerPlugin::OnDataReceived(const std::string &_srcAddress,
    const std::string &_data)
{
  gzmsg << "---" << std::endl;
  gzmsg << "[" << this->Host() << "] New message received" << std::endl;
  gzmsg << "\tFrom: [" << _srcAddress << "]" << std::endl;
  gzmsg << "\tData: [" << _data << "]" << std::endl;
}
```

This is the callback that we specified when we used the `Bind()` function. As you can see in the previous snippet, the address of the sender is available as a parameter, as wells as the data in `std::string` format. Note that a controller will receive its own messages when sending broadcast and multicast messages.