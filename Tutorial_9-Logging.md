# Overview

This tutorial will describe the Swarm logging features, how to use it, and how to read information from a log file after running a Gazebo experiment.

Gazebo already has logging capabilities but are slightly different from the Swarm logging described in this tutorial. A Gazebo log contains the state of the simulation during each cycle. In a nutshell, Gazebo logs the position and velocities of all the models during the whole simulation. However, it does not log information contained in plugins. The Swarm logging logs in a separate file data specifically interesting for the Swarm project, such as the sensor observations, the actions sent from the controller to the robot, or the communication exchange among the robots.

# Log's content

A Swarm log file contains a collection of log entries. Each log entry contains the following fields:

* **id** : Identity of the entity that wrote this log entry. In our case, the `id` are the addresses of the robots, or "boo", or "broker_plugin". 

* **time**: Simulation time at the moment of capturing this log entry.

* **sensors**: Contains the observed sensor measurements received by the RobotControllerPlugin during a cycle. E.g.: GPS, IMU, compass, camera, battery

* **actions**: Contains the actions sent to the vehicle by the team's controller plugin. E.g.: Apply a linear velocity of 1 m/s and an angular velocity of 0.5 rad/s.

* **incoming_msgs**: Contains the list of messages received by the entity **id** during a specific **time**. E.g.:

     * A message from `192.168.3.2` to `192.168.3.1:8000` of `100` bytes was received successfully.
     * A message from `192.168.3.3` to `broadcast:8000` of size `20` bytes was received successfully.
     * A message from `192.168.3.4` to `192.168.3.1:8000` of size `40` bytes was dropped.

* **visibility**: Contains a connectivity map of the entire swarm. For each pair of robots, we store the visibility status of the two robots:

     * VISIBLE: Both robots are not under communication outages, stay within range and there are no blocking obstacles between them. Note that a VISIBLE status does not mean that all communications between the two robots will be successful. A message can fail because of the random drops that we also apply to our communication model.
     * OUTAGE: Communication will fail because at least one of the robots is having a communication outage.
     * OBSTACLE: Communication will fail because there is one or more objects between the robots that according to our model, will cause communication between the robots fail.
     * DISTANCE: The robots are not within range, hence the communication will fail.

A log entry is declared as a Google Protocol Buffer. You can consult the declaration [here]().

# How to run Gazebo with Swarm logging enabled

Swarm logging is disabled by default. If you want to enable you have to set the environment variable `SWARM_LOG` to `1` and launch gzserver/gazebo:

SWARM_LOG=1 gzserver worlds/ground_simple_2.world --verbose

After some time, type CTRL-C for stopping your simulation.

A new Swarm log file has been created under `~/.swarm/logs/<timestamp>`. Go to the content of the newly created subdirectory. E.g.:

`cd .swarm/log/2015-09-30T17:35:42.085166`

Here, you should see your Swarm log file named `swarm.log`.

# How to introspect or parse a Swarm log file

A Swarm log file uses a binary format but you can use the `swarmlog` command line tool for introspecting the log in a human readable format.





