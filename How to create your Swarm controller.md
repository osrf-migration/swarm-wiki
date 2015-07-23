# Overview #

A Swarm controller is implemented as a model plugin attached to one of the robots under simulation. We have created a template that will help you writing your first controller.

# Write your first Swarm controller #

1. First, create a `swarm_controller` directory in your HOME folder:

        mkdir ~/swarm_controller; cd ~/swarm_controller

1. Download [CMakeLists.txt](https://s3.amazonaws.com/osrf-distributions/swarm/swarm_controller/CMakeLists.txt) file under `~/swarm_controller`.

1. Download [SwarmControllerPlugin.hh](https://s3.amazonaws.com/osrf-distributions/swarm/swarm_controller/TeamControllerPlugin.hh) file under `~/swarm_controller`.

1. Download [SwarmControllerPlugin.cc](https://s3.amazonaws.com/osrf-distributions/swarm/swarm_controller/TeamControllerPlugin.cc) file under `~/swarm_controller`.

1. Create a *build* directory:

        mkdir build; cd build

1. Build your plugin (controller):

        make

1. Install your plugin:

        sudo make install

**Important:** Do not change the name of the library `libTeamControllerPlugin.so`. The Swarm world files assume that your controller has this specific name. 

# Test your first Swarm controller #

Your first controller (*libSwarmControllerPlugin.so*) is ready to be tested. We assume that you have completed the [installation step](https://bitbucket.org/osrf/swarm/wiki/Installation) and your Swarm system is up and running.

Run Gazebo with a basic Swarm world file:

gazebo worlds/swarm_empty.world --verbose

You should see the following output on the console:


```
#!python
Gazebo multi-robot simulator, version 6.0.7
Copyright (C) 2012-2015 Open Source Robotics Foundation.
Released under the Apache 2 License.
http://gazebosim.org

[Msg] Waiting for master.
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Publicized address: 172.23.1.7
[Msg] Swarm controller plugin loaded successfully
[Msg] Swarm controller plugin loaded successfully

```

Note that there are two confirmation messages because this world file loads two models, hence two robot controllers will be loaded in Gazebo.