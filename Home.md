# Important links

1. [API](https://s3.amazonaws.com/osrf-distributions/swarm/api/0.1.0/index.html): Team controller API.

1. [Gazebo Help](http://answers.gazebosim.org): A forum for finding solutions and asking questions about 
Gazebo. Please directly ask OSRF for help, using the swarm mailing list, first. 

1. [Swarm mailing list](https://groups.google.com/a/osrfoundation.org/forum/#!forum/swarm): A mailing list for general communication, discussion, and help.

# Tutorials

1. [Install](https://bitbucket.org/osrf/swarm/wiki/Install.md)
1. [How to create your Swarm controller](https://bitbucket.org/osrf/swarm/wiki/Tutorial_1-How_to_create_your_Swarm_controller)
1. [Velocity control](https://bitbucket.org/osrf/swarm/wiki/Tutorial_2-Velocity_Control)
1. [Swarm communication API](https://bitbucket.org/osrf/swarm/wiki/Tutorial_3-Swarm_communication_API)
1. [GPS Sensor](https://bitbucket.org/osrf/swarm/wiki/Tutorial_4-GPS_sensor)
1. [Camera Sensor](https://bitbucket.org/osrf/swarm/wiki/Tutorial_5-Camera_sensor)
1. [IMU and compass Sensors](https://bitbucket.org/osrf/swarm/wiki/Tutorial_6-IMU_bearing_sensor)
1. [Batteries](https://bitbucket.org/osrf/swarm/wiki/Tutorial-batteries)
1. [Finding the person and reporting success](https://bitbucket.org/osrf/swarm/wiki/Tutorial_7-Find_person)
1. [Log your experiments](https://bitbucket.org/osrf/swarm/wiki/Tutorial_9-Logging)
1. [Upload log files](https://bitbucket.org/osrf/swarm/wiki/Tutorial_8-Upload_log_files)
1. [Run multiple Gazebo instances in parallel](https://bitbucket.org/osrf/swarm/wiki/Tutorial_10-Multiple_gazebo_instances)
1. [Terrain test plugin](https://bitbucket.org/osrf/swarm/wiki/Tutorial_11-Terrain_test_plugin)
1. [How to create your BOO controller](https://bitbucket.org/osrf/swarm/wiki/Tutorial_12-How_to_create_your_BOO_controller)
1. [How to create a custom world](https://bitbucket.org/osrf/swarm/wiki/Tutorial_13-Create_custom_world)
1. [Generate a PDF report using a log file](https://bitbucket.org/osrf/swarm/wiki/Tutorial_14-Generate_reports)

# Test worlds

1. `gazebo worlds/ground_simple_2.world`

    This will run a sample world with 2 ground vehicles.

1. `gazebo worlds/fixed_simple_36.world`

    This will run a sample world with 36 fixed wing aircraft.

1. `gazebo worlds/rotor_simple_36.world`

    This will run a sample world with 36 rotorcraft.

1. `gazebo worlds/ground_simple_36.world`

    This will run a sample world with 36 ground vehicles.

# Meetings

1. [June 29th, Kickoff](https://bitbucket.org/osrf/swarm/wiki/Kickoff_meeting)

# Swarm communication

* July 15th: The [Swarm_Communication](https://bitbucket.org/osrf/swarm/wiki/Swarm_Communication) page contains detail on the current communication plan.

# FAQ

1. How do I know I've detected the lost person?
> The camera sensor will detect an object with the name "lost_person".

1. Will the size of 'world' be known? (I.e., does my robot team know the GPS-coordinates of the search space?)
> Yes. The size of the world and GPS starting location will be provided.

1. Are there any other features of the environment that can be provided to the robots a-priori?
> You will be provided with a complete map of the environment, except for the location of the lost person. The format of the map is yet to be determined.

1. Will there be a fixed base-station with a known location?
> Yes, the base station (a.k.a base of operations) will be the same location as the start location.

1. Will you be providing a model of how GPS and camera (sensing data) are affected by the environment? (So that we can exploit this in our algorithms)
> We will provide statistical models of the robot's sensing and actuation, which will both be corrupted by noise. GPS will likely be modeled with noise that is independent of the environment. The accuracy of the camera will depend on the environment type and distance to objects.

1. The wiki mentions that different configurations of aerial and ground vehicles will be used. Will these configs be given, or do we decide? Also, does 'config' just mean proportion of ground-to-aerial, or are there more parameters that can be tuned? (I remember there was a similar question in the call, but don't recall your answer...)
> Initially the teams will choose the distribution of aerial and ground vehicles. OSRF will likely have you run a few specific distributions to have a consistent sampling between teams. The routing protocol will very likely be a configuration option that you can choose. Depending on the network simulator, you may also be able to choose between 802.11 and 802.15.4. All of this information will be included in the API and usage documentation.

1. How will power consumption be handled?
> We are still working out the fine details. Currently, each robot will have the concept of available power and power costs for locomotion and communication. Ground vehicles and fixed wing will have more power than rotorcraft.

1. Environments and line of sight.
> Line of sight between vehicles will be used to determine if communication is possible. Intervening terrain and buildings will prevent communication. Vegetation will degrade communication. 
