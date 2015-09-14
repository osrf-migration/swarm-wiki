# UNDER CONSTRUCTION

# Overview

This tutorial demonstrates a complete (though incredibly naive) solution to the lost-person problem.

We will employ the following strategy:

* For all ground and rotorcraft robots: random-walk through the environment, using a constant forward velocity combined with fixed-length periods of randomly selected rotational velocity.  We don't do anything with fixed-wing vehicles, because their control is more nuanced.
* Every time step in which a vehicle sees the lost person, it will:
    * Convert the person's pose as detected by its camera into the world frame (using an API provided in `RobotPlugin`).
    *  Construct a message with the following syntax: `FOUND <x> <y> <z> <t>`, where:
        * x, y, z: the position of the lost person, in the world frame (meters)
        * t: the time at which the person was observed at that location (seconds)
    * Send that message to the `kBroadcast` address on the `kBooPort` port.
* On receipt of any message, any vehicle that has not previously sent that message will relay it, addressed to (`kBroadcast`, `kBooPort`).  To keep track of what it has previously sent, each vehicle maintains a list of the payloads of each message that it sends, then on receipt of each new message does a linear search in that list to determine whether the incoming message should be relayed.

This strategy is obviously a poor one, with many deficiencies, including:

* no coordination of vehicle motion through the search area;
* no effort to actually cover the search area;
* no recollection of what area has already been searched; and
* no reasonable management of communications relaying.

