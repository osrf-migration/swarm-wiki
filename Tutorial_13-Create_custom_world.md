This tutorial describes how to create a custom world for swarm simulation.

The simplest approach is to modify the [`worlds/master.erb`](https://bitbucket.org/osrf/swarm/src/d88d64b8cb836282d67944da178b846372961fa9/worlds/master.erb?at=default&fileviewer=file-view-default) file. This file contains all the parameters for a swarm world in the [`default`](https://bitbucket.org/osrf/swarm/src/d88d64b8cb836282d67944da178b846372961fa9/worlds/master.erb?at=default&fileviewer=file-view-default#master.erb-4) function. 

To create your own world, you'll need to create a new function in which you override any or all of the default parameters. Then add the function to a hash at the end of `worlds/master.erb`, and re-install the swarm code. You should now have a new world file for use with your swarm control code.

## Step-by-step instructions

In this example, we'll just modify the start location of the lost person.

1. Add a new function for your world. In `worlds/master.erb`, add:

        def custom()
          default()
          $lost_person_x = 20
          $lost_person_y = 20   
        end

2. Add the function to the hash, located at the bottom of `worlds/master.erb`.

        h["custom.world"] = method(:custom)

3. Add the the world filename to the `generated_master_world_files` in `worlds/CMakeLists.txt`.

        set (generated_master_world_files custom.world
          ...
        )

3. Make and install the swarm code

        cd build
        make install

4. Run simulation

        gazebo worlds/my_custom_world.world