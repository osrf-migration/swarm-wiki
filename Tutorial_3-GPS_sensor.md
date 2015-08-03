# Overview

This tutorial will describe the GPS sensor, and how to access position information

# GPS

GPS in Gazebo uses a fixed frame of reference, and one or more sensors. The fixed frame of reference defines a location on Earth using spherical coordinates. The frame is defined in SDF:

    <spherical_coordinates>
      <latitude_deg>35.7753257</latitude_deg>
      <longitude_deg>-120.774063</longitude_deg>
      <elevation>208</elevation>
      <heading_deg>0</heading_deg>
    </spherical_coordinates>

A GPS sensor is attached to a robot, and reports its location by transforming the robot's metric pose to a latitude, longitude, and elevation. Each GPS sensor is defined by SDF:

    <sensor name="gps" type="gps">
      <gps/>
      <always_on>1</always_on>
    </sensor>

# The RobotPlugin API

Two location functions are provided:

1. [`RobotPlugin::Pose()`](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#a3d85d51691c5dd8e6b4ce7755dbe48d4) : Retrieves the robot's current pose from its GPS sensor.

1. [`RobotPlugin::SearchArea()`](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#aa670d09bce9e107693b81f8445547871) Get the search area, in GPS coordinates.

# Example usage

1. First, create a `swarm_controller2` directory:

        mkdir swarm_controller2
        cd swarm_controller2

1. Download the example files into the `swarm_controller2` directory:

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
