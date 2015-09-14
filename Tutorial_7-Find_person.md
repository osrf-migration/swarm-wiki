# UNDER CONSTRUCTION

# Overview

This tutorial demonstrates a complete (though incredibly naive) solution to the lost-person problem.  It's intended as an example and perhaps a baseline against which to compare more intelligent solutions.

## Strategy
We will employ the following strategy:

* **Random walk:** For all ground and rotorcraft robots: random-walk through the environment, using a constant forward velocity combined with fixed-length periods of randomly selected rotational velocity.  We don't do anything with fixed-wing vehicles, because their control is more nuanced.
* **Holler when you found the person:** Every time step in which a vehicle sees the lost person, it will:
    * Convert the person's pose as detected by its camera into the world frame (using an API provided in `RobotPlugin`).
    *  Construct a message with the following syntax: `FOUND <x> <y> <z> <t>`, where:
        * x, y, z: the position of the lost person, in the world frame (meters)
        * t: the time at which the person was observed at that location (seconds)
    * Send that message to the `kBroadcast` address on the `kBooPort` port.
* **Repeat everything you hear:** On receipt of any message, any vehicle that has not previously sent that message will relay it, addressed to (`kBroadcast`, `kBooPort`).  To keep track of what it has previously sent, each vehicle maintains a list of the payloads of each message that it sends, then on receipt of each new message does a linear search in that list to determine whether the incoming message should be relayed.

This strategy is obviously a poor one, with many deficiencies, including:

* no coordination of vehicle motion through the search area;
* no effort to actually cover the search area;
* no recollection of what area has already been searched; and
* no reasonable management of communications relaying.

Having said that, it will succeed sometimes, in very simple worlds.

# Example usage

1. First, create a `swarm_controller7 directory:

        mkdir swarm_controller7
        cd swarm_controller7

1. Download the example files into the `swarm_controller7` directory (**TODO: update these links after [PR24](https://bitbucket.org/osrf/swarm/pull-requests/24/tutorial_7) is merged**)

        wget https://bitbucket.org/osrf/swarm/raw/tutorial_7/tutorials/tutorial_7/CMakeLists.txt
        wget https://bitbucket.org/osrf/swarm/raw/tutorial_7/tutorials/tutorial_7/TeamControllerPlugin.hh
        wget https://bitbucket.org/osrf/swarm/raw/tutorial_7/tutorials/tutorial_7/TeamControllerPlugin.cc

1. Create a `build` directory:

        mkdir build
        cd build

1. Compile the controller:

        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make -j4
        sudo make install

1. Execute the controller:

        gazebo worlds/ground_easy_4.world --verbose

## Expected results

You should see 4 ground vehicles wondering around the world randomly.  If one of them sees the lost person (this will usually happen very quickly or not at all), it will broadcast that fact, including the perceived position and time, also printing a message like this:

    [192.168.2.2] I found the lost person.  Sending: FOUND 20 21.61 -0.2889 8.06

Other vehicles that hear the message will relay it, printing something like this:

    [192.168.2.3] Relaying message from 192.168.2.2 with payload FOUND 20 21.702 -0.33398 8.52

If the BOO receives the message and the reported position is within the configured position and time tolerance, then it will pause simulation and print a message to console like this:

    [Dbg] [BooPlugin.cc:264] Congratulations! Robot [192.168.2.2] has found the lost person at time [8 520000000]


