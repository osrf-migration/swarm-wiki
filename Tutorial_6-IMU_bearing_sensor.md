# Overview

This tutorial will describe the IMU and compass sensors, and how to access IMU and bearing information.

# IMU

An IMU uses a reference frame relative to the vehicle. The sensor is attached to a robot, and reports its linear (m/s) and angular velocity (rad/s), as well as the orientation relative to a reference pose (rad). In the Swarm project, the reference pose was initialized when the robot was spawned. Each IMU sensor is defined by SDF:

    <sensor name="imu" type="imu">
      <imu/>
      <always_on>1</always_on>
    </sensor>

# The RobotPlugin API

Two functions are provided:

1. [`RobotPlugin::Imu()`](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#a3d85d51691c5dd8e6b4ce7755dbe48d4) : Retrieves the robot's current pose from its GPS sensor.

1. [`RobotPlugin::Bearing()`](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#aa670d09bce9e107693b81f8445547871) Get the search area, in GPS coordinates.

# Example usage

1. First, create a `swarm_controller6 directory:

        mkdir swarm_controller6
        cd swarm_controller6

1. Download the example files into the `swarm_controller6` directory:

        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_6/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_6/TeamControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_6/TeamControllerPlugin.cc

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

```
[192.168.2.1] Lin. Vel: 0 0 0
[192.168.2.1] Ang. Vel: 0 0 0
[192.168.2.1] Orient.: 0 -0 0
[192.168.2.1] Bearing: 1.5708
[192.168.2.1] Lin. Vel: 1 -0.001 0
[192.168.2.1] Ang. Vel: 0 0 0
[192.168.2.1] Orient.: 0 -0 0
[192.168.2.1] Bearing: 1.5698
[192.168.2.1] Lin. Vel: 1 -0.001 0
[192.168.2.1] Ang. Vel: 0 0 0.1
[192.168.2.1] Orient.: 0 -0 0.001
[192.168.2.1] Bearing: 1.5688
```