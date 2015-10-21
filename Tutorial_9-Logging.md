# Overview

This tutorial will describe the Swarm logging features, how to use it, and how to read information from a log file after running a Gazebo experiment.

Gazebo already has logging capabilities but are slightly different from the Swarm logging described in this tutorial. A Gazebo log contains the state of the simulation during each cycle. In a nutshell, Gazebo logs the position and velocities of all the models during the whole simulation. However, it does not log information contained in plugins. The Swarm logging logs in a separate file data specifically interesting for the Swarm project, such as the sensor observations, the actions sent from the controller to the robot, or the communication exchange among the robots.

# Log's content

A Swarm log file is composed by a collection of log entries. Each log entry contains the following optional fields:

* **id** : Identity of the entity that wrote this log entry. In our case, the **id** is the address of the robot, or "boo", or "broker_plugin". 

* **time**: Simulation time at the moment of capturing this log entry.

* **sensors**: Contains the observed sensor measurements received by the RobotControllerPlugin during a cycle. E.g.: GPS, IMU, compass, camera, battery.

* **actions**: Contains the actions sent to the vehicle by the team's controller plugin. E.g.: Apply a linear velocity of 1 m/s and an angular velocity of 0.5 rad/s.

* **incoming_msgs**: Contains the list of messages sent during a specific **time**. E.g.:

     * A message from `192.168.3.2` to `192.168.3.1:8000` of `100` bytes was sent:
         * 192.168.3.1 received the message.
     * A message from `192.168.3.3` to `broadcast:8000` of size `20` bytes was sent:
         * 192.168.3.1 dropped the message.
         * 192.168.3.2 received the message.

* **visibility**: Contains a connectivity map of the entire swarm. For each pair of robots, we store the visibility status of the two robots:

     * VISIBLE: Both robots are not under communication outages, stay within range and there are no blocking obstacles between them. Note that a VISIBLE status does not mean that all communications between the two robots will be successful. A message can fail because of the random drops that we also apply to our communication model.
     * OUTAGE: Communication will fail because at least one of the robots is having a communication outage.
     * OBSTACLE: Communication will fail because there is one or more objects between the robots that according to our model, will cause communication between the robots fail.
     * DISTANCE: The robots are not within range, hence the communication will fail.

A log entry is declared as a Google Protocol Buffer. You can consult the declaration [here]().

# How to run Gazebo with Swarm logging enabled

Swarm logging is disabled by default. If you want to enable you have to set the environment variable `SWARM_LOG` to `1` and launch gzserver/gazebo:

`SWARM_LOG=1 gzserver worlds/ground_simple_2.world -r --verbose`

After some time, type CTRL-C for stopping your simulation.

A new Swarm log file has been created under `~/.swarm/log/<timestamp>`. Go to the content of the newly created subdirectory. E.g.:

`cd ~/.swarm/log/2015-09-30T17:35:42.085166`

Here, you should see your Swarm log file named `swarm.log`.

A new Gazebo log file has also been created in `~/.gazebo/log/<timestamp>/gzserver/state.log`. This log file and the `swarm.log` file should be saved. See the [tutorial on uploading log file](https://bitbucket.org/osrf/swarm/wiki/Tutorial_8-Upload_log_files).

# How to introspect or parse a Swarm log file

A Swarm log file uses binary codification but you can use the `swarmlog` command line tool for introspecting the log in a human readable format.

Type the following command for iterating through the list of log entries:

`swarmlog -s -f swarm.log`

You should the following output in your terminal:

```
#!python

id: "192.168.3.1"
time: 0.01
sensors {
  gps {
    latitude: 0
    longitude: 0
    altitude: 0
  }
  imu {
    linvel {
      x: 0
      y: 0
      z: 0
    }
    angvel {
      x: 0
      y: 0
      z: 0
    }
    orientation {
      x: 0
      y: 0
      z: 0
      w: 1
    }
  }
  bearing: 0
  image {
  }
  battery_capacity: 110000
}
actions {
  linvel {
    x: 0
    y: 0
    z: 0
  }
  angvel {
    x: 0
    y: 0
    z: 0
  }
}
incoming_msgs {
}
```

You can press space for jumping to the next log entry or `q` to quit.

Additionally, you can output the whole content of a Swam log file to screen:

`swarmlog -e -f swarm.log`

Or you can redirect the output to a file:

`swarmlog -e -f swarm.log > swarm.log.txt`

We have created a header file `LogParser.hh` that can help you if you need to parse a log file programmatically:

* *LogParser(const std::string &_filename)*: Class constructor that accepts the full path to the Swarm log file as a parameter.

* *bool Next(msgs::LogEntry &_entry)*: Read the next log entry from the log. If the return value is `true`, a new log entry was parsed successfully. You can read about the [Google Protocol Buffers C++ API](https://developers.google.com/protocol-buffers/docs/cpptutorial) for learning how to access the individual fields of the log entry in case you are not familiar with Protocol Buffers. If you are at the end of the log file or an unexpected problem happened while parsing the next entry, the function will return `false`.

We used `LogParser.hh` for writing `swarmlog`. You can also read the [`swarmlog.cc`]() source code as another log parsing example.





