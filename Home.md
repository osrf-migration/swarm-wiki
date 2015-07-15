# Meetings

1. [June 29th, Kickoff](https://bitbucket.org/osrf/swarm/wiki/Kickoff_meeting)

# Swarm communication

* July 15th: The [Swarm_Communication](https://bitbucket.org/osrf/swarm/wiki/Swarm_Communication) page contains detail on the current communication plan.

# FAQ

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
