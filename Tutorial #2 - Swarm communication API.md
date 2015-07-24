# Overview #

This tutorial will explain how to use the C++ Swarm client library for communication.

We assume that you have already done the [installation step](https://bitbucket.org/osrf/swarm/wiki/Installation) and you know [how to write your own custom controller](https://bitbucket.org/osrf/swarm/wiki/Tutorial%20%231%20-%20How%20to%20create%20your%20Swarm%20controller).

# The communication API explained ###

The Swarm client library contains three functions related with communications:

* **Bind(callback, address, port)**: This method binds an address and port to a virtual socket, and sends incoming messages to the specified callback.
* **SendTo(data, address, port)**: This method allows an agent to send data to other individual agent (unicast), all the agents (broadcast), or a group of agents (multicast). The addresses for sending broadcast and multicast messages are `kBroadcast` and `kMulticast` respectively.
* **Host()**: This method returns the address of the robot where the controller is running.

You can check the [C++ API](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/index.html) full documentation.

# Simple communication among agents #

Download the `swarm_comms_tutorial.tgz` file in your HOME directory and extract it:

        tar xvfz ~/swarm_comms_tutorial.tgz

Change to the new directory and create a *build* directory:

        cd swarm_comms_tutorial; mkdir build; cd build

Compile the controller:

        make -j4
        sudo make install

Execute the controller:

        gzserver worlds/swarm_empty.world --verbose

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

In this example, we are loading two vehicles with addresses `192.168.2.1` and `192.168.2.2`. If you open the world file distributed with your Swarm client library, you will be able to see model blocks and how the address and other parameters are specified:

```
#!python

<!-- Robot #1-->
<model name="robot1">
  <pose>0 1 0 0 0 0</pose>
  <include>
    <uri>model://cinder_block</uri>
  </include>

  <!-- Load the plugin to control this robot -->
  <plugin name="swarm_controller_1" filename="libTeamControllerPlugin.so">
    <address>192.168.2.1</address>
    <num_messages>1</num_messages>
    <type>ground</type>
  </plugin>
</model>

<!-- Robot #2-->
<model name="robot2">
  <pose>0 2 0 0 0 0</pose>
  <include>
    <uri>model://cinder_block</uri>
  </include>

  <!-- Load the plugin to control this robot -->
  <plugin name="swarm_controller_2" filename="libTeamControllerPlugin.so">
    <address>192.168.2.2</address>
    <num_messages>1</num_messages>
    <type>ground</type>
  </plugin>
</model>
```

Once a model loads the `TeamControllerPlugin`, its `Load()` function is executed. This function executes only one time and it is the recommended place for reading SDF parameters and binding to your local address. Do not try to do these tasks in the class constructor because some of the Gazebo components might not be ready.

```
#!python

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

As you can see in the previous snippet, we are using `HasElement()` and `Get()` functions for handling custom SDF parameters (in this case the parameter is `<num_messages>`. We also bind to our local and multicast addresses for receiving unicast and multicast messages. In this example we use the same callback but you could use different callbacks if you prefer to dispatch the messages differently.


After executing the `Load()` function, each controller periodically executes its `TeamControllerPlugin::Update()` function. In the following snippet we can see how the different messages are sent:

```
#!python

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

The first task that the controller does is to set the destination address. Then, the controller uses the function `SendTo()` for sending a unicast message, a broadcast message and a multicast message. Note that you don't need to `Bind()` for sending messages. `Bind()` is only used if you are interested on receiving messages. Finally, the following snippet shows the callback associated to the incoming messages:


```
#!python

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

This is the callback that we specified when we used the `Bind()` function. As you can see in the previous snippet, the address of the sender is available as a parameter, as wells as the data in std::string format. Note that a controller will receive its own messages when sending broadcast and multicast messages.
