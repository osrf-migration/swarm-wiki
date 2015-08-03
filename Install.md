# Overview

The Swarm setup is composed of a [Gazebo robot simulator]((www.gazebosim.org)), a [ns-3 network simulator](nsnam), a client library and a client controller. Gazebo simulates the environment, the robots, and the sensors, whereas ns-3 models and simulates the communication among the vehicles. The Swarm client library offers the communication, sensor access and motion API to the client controller. The client controller guides each simulated robot using the Swarm API.

# Install

## Prerequisites

1. Ubuntu 14.04, or later, 64-bit

1. `sudo apt-get install build-essential cmake mercurial libprotoc-dev protobuf-compiler ruby libignition-transport-dev`

## Gazebo

Detailed instructions locate [here](http://gazebosim.org/tutorials?tut=install_ubuntu&ver=6.0&cat=install), single line install follows.

1. `wget -O /tmp/gazebo6_install.sh http://osrf-distributions.s3.amazonaws.com/gazebo/gazebo6_install.sh; sudo sh /tmp/gazebo6_install.sh`

## ns-3

Follow the instructions described in the [ns-3 installation tutorial](https://www.nsnam.org/wiki/Installation).  You might find the [Bake installation method](https://www.nsnam.org/wiki/Installation#Installation_with_Bake) easiest.

## Swarm client library

1. Clone the repository into a directory in your home folder:

        hg clone https://bitbucket.org/osrf/swarm

1. Change directory in the Swarm repository:

        cd swarm

1. Create a *build* directory and go there:

        mkdir build
        cd build

1. Build and install:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

1. Change to the *example* directory and create a *build* folder:

        cd ../example
        mkdir build
        cd build

1. Build and install the client controller example:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

# Testing your installation

In the previous step we installed the Swarm client library `libSwarmRobotPlugin.so` and a client controller example `libTeamControllerPlugin.so`. Run the following command to test your installation:

        gzserver --verbose worlds/ground_simple_2.world

You should see the following output confirming that your installation succeeded, similar to:

```
#!python
[Msg] BrokerPlugin::ReadSwarmFromSDF: 2 swarm members found
[Msg] [192.168.2.1] Neighbors:
[Msg] 	192.168.2.2
[Msg] [192.168.2.1] search area: 35.7653 35.7853 -120.784 -120.764
[Msg] [192.168.2.1] lat long alt: 35.7753 -120.774 208.05
[Msg] [192.168.2.2] Neighbors:
[Msg] 	192.168.2.1
[Msg] ---
[Msg] [192.168.2.2] New message received
[Msg] 	From: [192.168.2.1]
[Msg] 	Data: [Unicast data]
[Msg] ---
```
