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
* no recollection of what area has already been searched;
* no reasonable management of communications relaying;
* unbounded memory consumption in the `messagesSent` list; and
* slow (linear) lookup of previously sent messages.

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

# How it works

The key parts of this controller are:

* In the `TeamControllerPlugin::Load()` method (which is called once during initialization before simulation starts) subscribe to messages that are sent to the `kBooPort` port.

        #!c++
        void TeamControllerPlugin::Load(sdf::ElementPtr _sdf)
        {
          // Sign up to receive unicast and broadcast messages
          this->Bind(&TeamControllerPlugin::OnDataReceived, this, this->Host(),
            this->kBooPort);
        }


* **Holler when you found the person:**  We check on every timestep whether we see the person, using the code below, which is part of the `TeamControllerPlugin::Update()` method.  Note that we get the list of objects from the logical camera using the `RobotPlugin::Image()` method, then if we found the person (by matching against the name `"lost_person"`), we call `RobotPlugin::CameraToWorld()` to convert the person's pose into the world frame.  Then we build the specially formatted `FOUND` message and broadcast on the `kBooPort` port.

        #!c++
        // Do we see the lost person?
        // Get the camera information
        ImageData img;
        if (this->Image(img))
        {
          for (auto const obj : img.objects)
          {
            if (obj.first.find("lost_person") != std::string::npos)
            {
              // Convert to the world frame.
              ignition::math::Pose3d personInWorld =
                this->CameraToWorld(obj.second);
              // Tell everybody about it.
              std::stringstream successMsg;
              successMsg << "FOUND " << personInWorld.Pos() << " " <<
                _info.simTime.Double();
              std::cout << "[" << this->Host() <<
                "] I found the lost person.  Sending: " << successMsg.str() <<
                std::endl;
              this->SendTo(successMsg.str(), this->kBroadcast, this->kBandooPort);
              // Remember this message for later, to avoid relaying it.
              this->messagesSent.push_back(successMsg.str());
            }
          }
        }

* **Random walk:** We use a simple state machine, implemented in the code below, which is part of the `TeamControllerPlugin::Update()` method.  We're always moving forward at 1m/s.  Every 10s, we pick a new random yaw velocity in the range [-0.5rad/s, 0.5rad/s].  After 2.5s (10/4), we set the yaw velocity back to zero to drive straight.  Then 7.5s later (the remainder of the 10s period), we repeat the procedure.  Note that we remember the last computed commands and resend them every cycle; this is needed to avoid having the robot stop moving.

        #!c++
        // Do a random walk, changing direction every once in a while.
        gazebo::common::Time changePeriod(10, 0);
      
        if ((this->lastCmdTime == gazebo::common::Time::Zero) ||
            ((_info.simTime - this->lastCmdTime) > changePeriod))
        {
          // Time has elapsed; time to pick new velocities.

          // Constant forward velocity (X)
          ignition::math::Vector3d linVel(1,0,0);
          // Bounded angular velocity (yaw about Z)
          ignition::math::Vector3d angVel(0, 0,
            ignition::math::Rand::DblUniform(-0.5, 0.5));
          //std::cout << "[" << this->Host() << "] Changing velocity to (" <<
            //linVel.X() << ", " << angVel.Z() << ")" << std::endl;
          this->SetLinearVelocity(linVel);
          this->SetAngularVelocity(angVel);
          this->lastLinVel = linVel;
          this->lastAngVel = angVel;
          this->lastCmdTime = _info.simTime;
        }
        else if ((_info.simTime - this->lastCmdTime) > changePeriod/4.0)
        {
          // Part of the time has elapsed; start going forward (to avoid just
          // doing circles).
          this->SetLinearVelocity(this->lastLinVel);
          this->lastAngVel.Z(0);
          this->SetAngularVelocity(this->lastAngVel);
        }
        else
        {
          // Not enough time has elapsed; send the last command again (otherwise
          // the robot will stop after one time step).
          this->SetLinearVelocity(this->lastLinVel);
          this->SetAngularVelocity(this->lastAngVel);
        }
        break;
      }

* **Repeat everything you hear:** We implement a relay-everything comms strategy in `TeamControllerPlugin::OnDataReceived()`, which will be called on each received message.  To avoid infinite loops (which could happen even with a single robot, because you hear your own messages), we keep a flat list of the payloads of all previously sent messages, and decide whether to relay an incoming message by first comparing it to each previously sent message.

        #!c++
        void TeamControllerPlugin::OnDataReceived(const std::string &_srcAddress,
            const std::string &_data)
        {
          // Totally dumb mesh network strategy: relay everything you hear that you
          // haven't previously relayed.
          auto alreadySent = false;
          for (auto const msg : this->messagesSent)
          {
            if (msg == _data)
            {
              alreadySent = true;
              break;
            }
          }
 
          if (!alreadySent)
          {
            // I haven't sent this before; relay it.
            std::cout << "[" << this->Host() << "] Relaying message from " <<
              _srcAddress << " with payload " << _data << std::endl;
            // At this point, I'd like to have the original dest address.  I'll just
            // assume that it should go to (kBroadcast, kBooPort).
            this->SendTo(_data, this->kBroadcast, this->kBooPort);
            // Remember this message for later.
            this->messagesSent.push_back(_data);
          }
          else
          {
            // I've sent this already; don't relay it.
 
            //std::cout << "[" << this->Host() << "] NOT relaying message from " <<
              //_srcAddress << " with payload " << _data << std::endl;
          }
        }