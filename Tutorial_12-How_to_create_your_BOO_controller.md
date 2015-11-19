# Overview

A BOO controller is implemented as a model plugin attached to the stationary Base of Operations. We have created a template that will help you writing your controller.

# Write your BOO controller

1. First, create a `boo_controller` directory:

        mkdir boo_controller
        cd boo_controller

1. Download the example files into the `boo_controller` directory:

        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_12/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_12/BooControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_12/BooControllerPlugin.cc

1. Create a `build` directory:

        mkdir build
        cd build

1. Build your plugin (controller):

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4

1. Install your plugin:

        sudo make install

**Important:** Do not change the name of the library `libBooControllerPlugin.so`. The Swarm world files assume that your controller has this specific name. 

# Test your Boo controller #

Your first controller (*libBooControllerPlugin.so*) is ready to be tested. We assume that you have completed the [installation step](https://bitbucket.org/osrf/swarm/wiki/Install) and your Swarm system is up and running.

Run Gazebo with a basic Swarm world file:

        gazebo worlds/complete_10.world --verbose

You should see the following output on the console:


```
#!python
[Msg] Waiting for master.
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Connected to gazebo master @ http://127.0.0.1:11345
[Msg] Publicized address: 172.23.3.69
[Msg] Publicized address: 172.23.3.69
[Msg] BrokerPlugin::ReadSwarmFromSDF: 31 swarm members found
Boo plugin loaded
```