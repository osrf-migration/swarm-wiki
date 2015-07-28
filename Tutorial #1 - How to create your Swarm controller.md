# Overview #

A Swarm controller is implemented as a model plugin attached to one of the robots under simulation. We have created a template that will help you writing your first controller.

# Write your first Swarm controller #

1. First, create a `swarm_controller` directory:

        mkdir swarm_controller
        cd swarm_controller

1. Download the example files into the `swarm_controller` directory: **TODO: After pull request #6 is merged, replace this download with `wget` calls to the appropriate place in the repo.**

        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_1/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_1/TeamControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_1/TeamControllerPlugin.cc

1. Create a `build` directory:

        mkdir build
        cd build

1. Build your plugin (controller):

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4

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