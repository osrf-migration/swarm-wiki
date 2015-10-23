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