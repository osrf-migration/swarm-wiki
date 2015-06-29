# Goals #

1. Analyze and report on how performance and communication scales with number of robots in a search-and-rescue task.
1. Questions we need to answer:
    1. Are we capable of controlling and using swarms now, as opposed to 10 years ago?
    1. When and why does a swarm break-down?
    1. What are the technological Mt. Everests? Communication, shared knowledge? 

# Timeline #

**June** Communication report and planning

1. Research and report on communication models and network simulators
1. Work with teams to develop simulation and API specifications

**July** Swarm Sim 1.0

1. Reduced physics and simplified robot models
1. Use existing environment models
1. Developer API version 1.0 and accompanying documentation
1. Release Swarm Sim 1.0 to teams

**August** Swarm Sim 2.0

1. Simplified environmental model
1. Partial integration of network simulation
1. Developer API version 2.0
1. Support teams and incorporate feedback

**September** Swarm Sim 3.0

1. Lost person model
1. Full communication model
1. Developer API version 3.0
1. Data logging
1. Support teams and incorporate feedback

**October - November** Conduct experiments

1. Support teams and Swarm Sim
1. Teams: run experiments to understand how performance scales with robot count

**December** Final report

1. Analyze the results and summarize the scientific and technical findings


# Software Development and Distribution #

1. Components

    1. Simulation: Gazebo
    1. Team interface to simulation: Gazebo plugin

1. Gazebo plugin API

    1. API development will be conducted in this repository

1. Software distribution

    1. OSRF will distribute prerelease debians of Gazebo for use with the swarm project
    1. Teams may also build Gazebo from source

# Simulation Components #

1. Environment
    1. **Goal**: Represent realistic outdoor terrain with different properties (trees, grassland, hills). These properties affect mobility, sensing, and communication

    1. **Optimal case**: Full 3D environment
    1. **Minimal case**: 2D property map representation

1. Robot model

    1. **Goal**: Simulate a large number (>100) aerial and ground autonomous vehicles performing a search and rescue task.

    1. **Physics**: Kinematic only entities that are controlled via position or velocity commands.
    1. **Collisions**: Only check for collisions with the ground.
    1. **Sensors**:
        1. GPS
        1. Logical camera: Reports location and type of objects seen within a viewing frustum. Information will be adjusted according to terrain properties
    1. **Radio**: Use ns-3 to simulate communication between vehicles
 
1. Lost person

    1. **Goal**: Simulate realistic behavior of a lost person.

    1. **Implementation**: Work with Mike Goodrich. We'll likely create a simple person model (random walk) to get everyone started.



