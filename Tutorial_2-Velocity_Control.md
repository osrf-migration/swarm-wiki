# Overview

This tutorial demonstrates the use of velocity control to move a simulated robot.

# Velocity control

All the robot models respond to the following API functions:

1. [RobotPlugin::SetLinearVelocity()](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#a73737d9c1eca8455006dabd76477d8b7) : Set the robot's linear velocity.
>
> The velocity is applied in the robot's local coordinate frame, where
>
> * x = forward/back,
> * y = left/right,
> * z = up/down.
>
> This velocity will be constrained by the type of robot. For example, a ground vehicle will ignore the y & z components of the _velocity vector, but a rotorcraft will use all three.
>
2. [RobotPlugin::SetAngularVelocity()](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/classswarm_1_1RobotPlugin.html#a4d9d82f1d5dd4cdfa4fe6096dc5ba573) : Set the robot's angular velocity, using Euler angles.
>
> The velocity is applied in the robot's local coordinate frame, where
>
> * x = rotate about x-axis (roll),
> * y = rotate about y-axis (pitch),
> * z = rotate about z-axis (yaw).
>
> This velocity will be constrained by the type of robot. For example, a ground vehicle will ignore the x and y components of the _velocity vector, but a quadcopter will use all three.

# Example usage:

1. First, create a `swarm_controller2` directory:

        mkdir swarm_controller2
        cd swarm_controller2

1. Download the example files into the `swarm_controller2` directory:

        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_2/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_2/TeamControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/default/tutorials/tutorial_2/TeamControllerPlugin.cc

1. Create a `build` directory:

        mkdir build
        cd build

1. Compile the controller:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

1. Execute the controller:

        gazebo worlds/ground_simple_2.world --verbose

You should see the robots moving in a circle.

