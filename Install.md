# Overview

The Swarm setup is composed of a [Gazebo robot simulator](http://gazebosim.org), a client library and a client controller. Gazebo simulates the environment, the robots, the communications, and the sensors. The Swarm client library offers the communication, sensor access and motion API to the client controller. The client controller guides each simulated robot using the Swarm API.

# Install

## Prerequisites

1. Ubuntu 14.04, or later, 64-bit

1. Setup packages.osrfoundation.org

    1. ```sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-latest.list'```

    1. `wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -`

    1. `sudo apt-get update`

1. `sudo apt-get install build-essential cmake mercurial libprotoc-dev protobuf-compiler ruby libignition-transport0-dev libignition-math2-dev`

## Gazebo

Detailed instructions located [here](http://gazebosim.org/tutorials?tut=install_ubuntu&ver=6.0&cat=install), single line install follows.

1. `wget -O /tmp/gazebo6_install.sh http://osrf-distributions.s3.amazonaws.com/gazebo/gazebo6_install.sh; sudo sh /tmp/gazebo6_install.sh`

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
[Msg] Waiting for master.
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Publicized address: 172.23.1.7
[Msg] BrokerPlugin::ReadSwarmFromSDF: 3 swarm members found
[Msg] [192.168.3.1] TeamController plugin loaded succesfully
[Msg] [192.168.3.2] TeamController plugin loaded succesfully

```
