# Overview #

The Swarm project aims to study the problem of collective control of large teams (*swarms*) of air and ground robots. Our focus is the scalability of such teams, in particular with regard to: (i) communication systems and (ii) coordination algorithms. The study will take place in simulation and the simulated task will be cooperative search for one or more lost persons in an outdoor environment.

The Swarm setup is composed of a [Gazebo robot simulator]((www.gazebosim.org)), a [ns-3 network simulator](nsnam), a client library and a client controller. Gazebo simulates the environment, the robots, and the sensors, whereas ns-3 models and simulates the communication among the vehicles. The Swarm client library offers the communication, sensor access and motion API to the client controller. The client controller guides each simulated robot using the Swarm API.

# Gazebo installation #

Before installing Gazebo, you need a machine with [Ubuntu](http://www.ubuntu.com/) 14.04 64-bit or later installed. Then, follow the instructions detailed in the [Gazebo installation tutorial](http://gazebosim.org/tutorials?cat=install).

# ns-3 installation #

Follow the instructions described in the [ns-3 installation tutorial](https://www.nsnam.org/wiki/Installation).

# Swarm client library #

The Swarm client library is a [model plugin](http://gazebosim.org/tutorials?tut=plugins_hello_world) that is the base class for all client controllers. A model plugin is a chunk of code that is compiled as a shared library, inserted into the simulation, and attached to a model. This plugin controls the model in simulation. The Swarm client library exposes the following functionality to the client controller:

* Configuration
    * Load(): This function allows the agent to read SDF parameters from the model.
* Communication
    * Bind(): This function binds an address to a virtual socket, and sends incoming messages to the specified callback.
    * SendTo(): This function allows an agent to send data to other individual agent (unicast), all the agents (broadcast), or a group of agents (multicast).
    * Host(): This function returns the agent's address.
* Motion
    * (*to be announced*)
* Sensors
    * (*to be announced*)

## Option 1: One-line install ##
   (to be announced)

## Option 2: Install from source ##

### Install Required Dependencies ###

1. Setup your computer to accept software from *packages.osrfoundation.org*:
  
        sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-latest.list'
  
1. Setup keys:

        wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -


1. Install prerequisites. A clean Ubuntu system will need:


        sudo apt-get install build-essential cmake mercurial libprotoc-dev protobuf-compiler ignition-transport


### Build and install Swarm client library ###

1. Clone the repository into a directory in your home folder:

        cd ~; hg clone https://bitbucket.org/osrf/swarm

1. Change directory in the Swarm repository:

        cd ~/swarm

1. Create a *build* directory and go there:

        mkdir build
        cd build

1. Build and install:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

1. Change to the *example* directory and create a *build* folder:

        cd ../../example
        mkdir build
        cd build

1. Build and install the client controller example:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

# Testing your installation #

In the previous step we installed the Swarm client library `libSwarmRobotPlugin.so` and a client controller example `libTeamControllerPlugin.so`. Run the following command to test your installation:

        gzserver worlds/swarm_test.world --verbose

You should see the following output confirming that your installation succeed:

```
#!python

Gazebo multi-robot simulator, version 6.0.7
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
