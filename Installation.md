# Overview #

The Swarm project aims to study the problem of collective control of large teams (*swarms*) of air and ground robots. Our focus is the scalability of such teams, in particular with regard to: (i) communication systems and (ii) coordination algorithms. The study will take place in simulation and the simulated task will be cooperative search for one or more lost persons in an outdoor environment.

The Swarm setup is composed of a [Gazebo robot simulator]((www.gazebosim.org)), a [ns-3 network simulator](nsnam), a client library and a client controller. Gazebo simulates the environment, the robots, and the sensors, whereas ns-3 models and simulates the communication among the vehicles. The Swarm client library offers the communication, sensor access and motion API to the client controller. The client controller guides each simulated robot using the Swarm API.

# Gazebo installation #

*Note: Gazebo 6 is required.  It will be officially released soon.*

Before installing Gazebo, you need a machine with [Ubuntu](http://www.ubuntu.com/) 14.04 64-bit or later installed. Then, follow the instructions detailed in the [Gazebo installation tutorial](http://gazebosim.org/tutorials?cat=install).  If you choose the step-by-step installation, be sure to install the extra `libgazebo6-dev` package.

# ns-3 installation #

*Note: ns-3 isn't being used yet; the installation step is included here for future use.*

Follow the instructions described in the [ns-3 installation tutorial](https://www.nsnam.org/wiki/Installation).  You might find the [Bake installation method](https://www.nsnam.org/wiki/Installation#Installation_with_Bake) easiest.

# Swarm client library #

The Swarm client library is a [model plugin](http://gazebosim.org/tutorials?tut=plugins_hello_world) that is the base class for all client controllers. A model plugin is a chunk of code that is compiled as a shared library, inserted into the simulation, and attached to a model. This plugin controls the model in simulation. The Swarm client library exposes the following functionality to the client controller:

* Configuration
    * Load(): This function allows the agent to read SDF parameters from the model.
* Communication
    * Bind(): This function binds an address to a virtual socket, and sends incoming messages to the specified callback.
    * SendTo(): This function allows an agent to send data to other individual agent (unicast), all the agents (broadcast), or a group of agents (multicast).
    * Host(): This function returns the agent's address.
    * Neighbors(): This method returns the addresses of other vehicles that are inside the communication range of this robot.
* Motion
    * Type(): This method returns the type of vehicle where this controller is running.
    * SetLinearVelocity(): New linear velocity applied to the robot.
    * SetAngularVelocity(): New angular velocity applied to the robot.
* Sensors
    * GetPose(): Get the robot's current pose from its GPS sensor.
    * GetSearchArea(): Get the search area, in GPS coordinates.

## Option 1: One-line install ##
   (to be announced)

## Option 2: Install from source ##

### Install Required Dependencies ###

1. Set up your computer to accept software from *packages.osrfoundation.org*:
  
        sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-latest.list'
  
1. Set up keys:

        wget http://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -


1. Install prerequisites. A clean Ubuntu system will need:

        sudo apt-get install build-essential cmake mercurial libprotoc-dev protobuf-compiler libignition-transport-dev libignition-math2-dev

### Build and install Swarm client library ###

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

# Testing your installation #

In the previous step we installed the Swarm client library `libSwarmRobotPlugin.so` and a client controller example `libTeamControllerPlugin.so`. Run the following command to test your installation:

        gzserver worlds/ground_simple_2.world

You should see the following output confirming that your installation succeed:

```
#!python
[192.168.2.1] TeamController plugin loaded
[192.168.2.2] TeamController plugin loaded
```
