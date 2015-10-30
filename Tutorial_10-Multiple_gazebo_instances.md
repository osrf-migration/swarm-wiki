Each Gazebo instance should use a different port for handling communications. You can specify the IP address and port with the environment variable *GAZEBO_MASTER_URI*.

Open a terminal and execute:

```
#!python

GAZEBO_MASTER_URI=http://localhost:11346 gazebo worlds/complete.world
```

Open another terminal and execute:

```
#!python

GAZEBO_MASTER_URI=http://localhost:11347 gazebo worlds/complete.world
```

You should see two independent Gazebo instances running. The previous instructions also apply for running `gzserver` or `gzclient`.

## Modify physics properties

Simulating a Swarm world is a CPU intensive task, specially if the number of robots increases. It is possible to change some of the physics parameters for trying to reduce the time required to run an experiment. 

* `max_step_size`: Specifies the time duration in seconds of each physics update step.
* `real_time_update_rate`: Specifies in Hz the number of physics updates that will be attempted per second. If this number is set to zero, it will run as fast as it can. Note that the product of real time update rate and max step size represents the target real time factor, or ratio of simulation time to real-time.

If Gazebo can maintain real-time during your experiment, try setting `real_time_update_rate` to 0.

If Gazebo cannot maintain real-time, you can experiment increasing `max_step_size`. Be aware that your controller updates will be less frequent, which might make the control of the vehicles harder.

You can change these parameters using the Gazebo physics properties GUI on the left panel. Alternatively, you can modify these parameters in the provided world files.


```
#!python

<physics type="ode">
  <ode>
    <solver>
      <type>world</type>
    </solver>
  </ode>
  <max_step_size>0.01</max_step_size>
  <real_time_update_rate>100</real_time_update_rate>
</physics>
```
