**Note: requires pending pull request: 

# Overview

This tutorial demonstrates how to use an experimental Python API for writing your swarm robot controller.

The idea is that you write a Python file (more generally, a module) that includes functions for loading, updating, and receiving messages. Then you configure a C++ plugin to call those functions.

The code described here is in the [tutorial_9 directory](https://bitbucket.org/osrf/swarm/src/add_python_api/tutorials/tutorial_9/?at=add_python_api).

# The C++ piece

To use Python, you still need to provide a C++ plugin, but it can be very short, just getting things setup to use your Python code, for example:

~~~
//////////////////////////////////////////////////
void TeamControllerPlugin::Load(sdf::ElementPtr _sdf)
{
  this->LoadPython("mycontroller", "myload", "myupdate", "myondatareceived");
}

//////////////////////////////////////////////////
void TeamControllerPlugin::Update(const gazebo::common::UpdateInfo &_info)
{
  this->UpdatePython(_info);
}
~~~

In the `Load` function, which is called once for each vehicle before the run starts, you need to call `LoadPython()` and pass it 4 strings, in the following order:

* The name of your Python file/module. This string will be passed directly to Python's `import`. E.g., if you have your code in a file called `mycontroller.py`, then you'd give `mycontroller` as the file/module name. All the functions named in the following arguments are assumed to exist within that file/module.
* The name of the function that should be called when your vehicle is loaded.
* The name of the function that should be called when your vehicle is updated, once per loop.
* The name of the function that should be called when your vehicle receives a message.

More to come below on the signatures of those functions.

In the `Update` function, you just need to call `UpdatePython()` and pass it the same `_info` argument that you received.

# The Python piece

Overall, the Python API very much mirrors the C++ API. Some helpful hints:

* The Python API that you can call is provided in the `swarm` module. In your Python code, you should `import swarm`, then call functions from there.
* The Python API is *not* object-oriented (it could be made so; pull requests welcome). As a result, both functions that you define and functions defined in Gazebo have as their first argument `me`, which is the string name of the vehicle being operated on. Where in C++ you might do `this->Launch();`, in Python you'd do `swarm.launch(me)`, assuming that you have the right value for `me` at that point in the code.
* Function names in C++ are translated into Python by making them lower case and separating words with underscores.
* Structured types in C++ (e.g., poses) are usually unrolled into lists in Python.
* The `swarm.bind()` function (equivalent to C++ `Bind()`) does not allow you to specify the name of the callback function to be used. All messages delivered as the result of any `swarm.bind()` call will be passed to the `ondatareceived()` callback that you specified in the C++ `LoadPython()` call.

## Functions of yours that are called

The following functions are defined by you (they don't have to use these names, so long as your Python code agrees with what you passed to `LoadPython()` in your C++ code):

* `load(me)`: Called once to initialize your vehicle. You might want to save the value of `me` for later use.
* `update(me, world_name, sim_time, real_time)`: Called on every simulation step. The arguments following `me` are just the contents of the `gazebo::common::UpdateInfo` structure that's normally passed to your C++ `Update()` function.
* `ondatareceived(me, src_address, dst_address, dst_port, data)`: Called when a message is delivered. The arguments following `me` are the same arguments usually passed to your C++ `OnDataReceived()` function.

## Functions that you can call

Most of the C++ API is exposed in Python and will hopefully behave as you expect. Here's an example Python program that exercises that API (the `swarm.gzmsg()` calls could be replaced with `print()` if you prefer):

~~~
import swarm
    
def myload(me):
    swarm.gzmsg('[%s] load()'%(me))
    swarm.gzmsg('[%s] vehicle type: %s'%(me, swarm.type(me)))
    search_area = swarm.search_area(me)
    swarm.gzmsg('[%s] search area: [%f, %f, %f, %f]' %
      (me, search_area[0], search_area[1], search_area[2], search_area[3]))
    lost_person_dir = swarm.lost_person_dir(me)
    swarm.gzmsg('[%s] lost person dir: [%f, %f]' %
      (me, lost_person_dir[0], lost_person_dir[1]))
    boo_pose = swarm.boo_pose(me)
    swarm.gzmsg('[%s] boo pose: [%f, %f]' %
      (me, boo_pose[0], boo_pose[1]))

    swarm.bind(me, swarm.host(me), 1234)
    swarm.bind(me, "multicast", 1234)
    
def myupdate(me, world_name, sim_time, real_time):
    swarm.gzmsg('[%s] update()'%(me))
    for n in swarm.neighbors(me):
      swarm.send_to(me, "unicast to %s"%(n), n, 1234)
    swarm.send_to(me, "broadcast to everyone", "broadcast", 1234)
    swarm.send_to(me, "multicast to some", "multicast", 1234)

    swarm.set_linear_velocity(me, 0.5, 0, 0)
    swarm.set_angular_velocity(me, 0, 0, 0.25)
    pose = swarm.pose(me)

    swarm.gzmsg('[%s] pose: [%f, %f, %f]'%(me, pose[0], pose[1], pose[2]))

    imu = swarm.imu(me)
    swarm.gzmsg('[%s] imu: [%f, %f, %f, %f, %f, %f, %f, %f, %f]' %
      (me, imu[0], imu[1], imu[2],
       imu[3], imu[4], imu[5],
       imu[6], imu[7], imu[8]))
    
    bearing = swarm.bearing(me)
    swarm.gzmsg('[%s] bearing: %f'%(me, bearing))

    image = swarm.image(me)
    swarm.gzmsg('[%s] image: %s'%(me, image))

    swarm.gzmsg('[%s] terrain type: %s'%(me, swarm.terrain_type(me)))

    swarm.gzmsg('[%s] docked?: %d'%(me, swarm.is_docked(me)))

    swarm.launch(me)
    swarm.dock(me, me)

def myondatareceived(me, src_address, dst_address, dst_port, data):
    swarm.gzmsg('[%s] ondatareceived(): %s %s %d %s'
          %(me, src_address, dst_address, dst_port, data))
~~~

# Trying it out

To try out the Python API:

* Build and install Swarm as usual, including the `examples` directory (needed to get a default BOO plugin installed).
* Build and install tutorial_9:

        cd swarm/tutorials/tutorial_9
        mkdir build
        cd build
        cmake .. -DCMAKE_INSTALL_PREFIX=/usr
        make
        sudo make install

* Adjust your `PYTHONPATH` so that `mycontroller.py` can be found, then run the simulation

        cd swarm/tutorials/tutorial_9
        export PYTHONPATH=`pwd`:$PYTHONPATH
        gazebo  --verbose worlds/ground_easy_4.world

You should see the four robots wandering around in circles, along with lots of console output resulting from the debug messages in the Python code.