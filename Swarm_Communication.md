Introduction
============

In this page, we describe plans for simulating robot
swarms, with particular attention to which aspects of communication are
simulated, and how they are modeled.

This content is a summary of a [detailed communication report](https://bytebucket.org/osrf/swarm/raw/603c179f3d151d8cd41f83a9b67f3a26fa655daf/docs/comm_report.pdf).

RF Propagation
====

Fine details of RF propagation will be difficult to simulate. Instead of
modeling RF propagation, or even the MAC layer above it, we will apply a
gross statistical model that is bad enough. Real-world networks,
especially large networks, have packet loss and network outages. A
statistical model will be used to simulate an unreliable network.

The statistical model will consist of 10%-50% packet loss, periods of
100% packet loss, and will take into account physical obstacles. Terrain
or buildings, which vehicles can not enter, that block line of sight
between vehicles will result in no communication between those vehicles.
Vegetation, such as trees, will increase packet loss.

Routing
===

The goal of this project is to explore the limits of swarm size, and
determine when and how a swarm breaks down. MANET algorithms, which try
to make a fully-connected network, are known to break down at about
N=100. Using a MANET network would therefore limit the swarm size
without exploring control limits or physical link limits.

The plausibility of deploying a simulated swarm in the real world, using
existing radio technology, also must be maintained. In order to meet
this constraint and avoid the MANET limit, a simple and plausible
alternative will be used that consists of UDP packets with a nearest
neighbor list. Communication between vehicles will use UDP, and each
radio will have a list of one-hop neighbors. The set of one-hop
neighbors determines who a given vehicle can communicate with. Any
additional routing logic will be built by the swarm control teams. This
strategy supports flexible swarm control and communication strategies,
while being grounded in reality.

Simulation plan
===============

The experimental phase of this project will be achieved by running
multiple simulation episodes with different number of robots,
communication protocols and cooperative strategies. Each simulation
experiment requires the definition of the number of robots involved and
its type (ground vehicles, fixed-wings, rotorcraft), the selection of
communication parameters, and the ability to execute team's code.

We will use ns-3 tosimulate communication, offering the following features:

-   propagation and path loss models, including interactions with
    terrain;

-   802.11 and 802.15.4 physical and data link layers;

-   one-hop nearest neighbor list; and

-   UDP transport.

Atop this stack of network simulation, developers of coordination
algorithms will be presented with a communication API that supports
sending and receiving unicast, multicast, and broadcast UDP messages.
This interface will resemble the send / receive interface provided by
many operating systems. 

Gazebo offers a flexible architecture for running different environments
and configurations via SDF world files. A world file is a test file
that allows Gazebo to spawn models and to specify plugins that will
modify model behavior in the simulated world. The teams will write these
plugins using an API that OSRF will develop for this study. The plugins
will allow the vehicle controllers to access their sensor information
(images and GPS data), send motion commands, and send and receive
messages.

Once the teams have the source code for their plugins ready, it will be
possible to launch different experiments with different parameters
without having to recompile code. For example, we can run one experiment
with 50 ground vehicles, 30 fixed-wind vehicle, and 20 rotorcraft, then
run again with a different population size and mix. Each
simulation episode will optionally log information that might be useful
for experiment post-processing. 

Internally, Gazebo will orchestrate the periodic execution of an
<span>Update()</span> function on each model, the generation of sensor
readings for each robot and the simulation of a time window.
Additionally, Gazebo will trigger and pause the network event simulation
during the same time window under simulation.

Finally, a log playback component will be developed for Gazebo and the
Swarm project. This piece of software will work in two phases. First, it
will be able to intercept all the API calls to the swarm API, and will
record them into disk. Every log entry will have a timestamp to capture
the exact simulation time where the call was requested. In a second
phase and after an experiment, a team will have the option of playing
back an experiment from the log previously saved. In this case, the log
entries will take place of the regular code executed in the
<span>Update()</span> method of the team's plugin. Teams will be able to
step forward, step back, pause or jump to a particular simulation
instant for debugging or visualization purposes.


