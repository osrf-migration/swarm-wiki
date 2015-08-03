# Overview

This tutorial will describe the camera sensor, and how to detect gather information about other objects in the environment.

# Camera

A logical camera is used to detect robots and other objects, such as a lost person, that fall within a specified view frustum. The logical_camera does not generate pixel images. Instead a list of detected objects along with their relative poses is generated.

* [SDF specification](http://sdformat.org/spec?ver=1.5&elem=sensor#sensor_logical_camera)

* [Gazebo API]()

# The RobotPlugin API

The output from a robot's logical camera is accessed via:

1. [`RobotPlugin::Image()`](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#a8d0e59606ce14a970350c4f771c43682) : Retrieves the set of objects detected by the camera.

# Example usage

1. First, create a `swarm_controller4` directory:

        mkdir swarm_controller4
        cd swarm_controller4

1. Download the example files into the `swarm_controller4` directory:

        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_4/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_4/TeamControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_4/TeamControllerPlugin.cc

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

