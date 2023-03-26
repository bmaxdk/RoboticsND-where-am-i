# RoboticsND-where-am-i
## Project 3: Where Am I

Application of using the adaptive monte carlo localizatin (AMCL) algorithm is implemented in ROS. AMCL dynamically adjusts the number of particles over a period of time, as the robot navigates around in a map. The process of AMCL algorithm is followed by the [amcl](http://wiki.ros.org/amcl) ROS package to get a working localization.

[image1]: img/p1.png "img1"
[image2]: img/p2.png "img2"
[image3]: img/f1.png "img3"


### Result
Initial Condition 
![alt text][image1]

Processing Condition
![alt text][image2]

Final Result
![alt text][image3]




## Project Overview
Following package need to be installed
```bash
$ sudo apt-get install ros-kinetic-navigation
$ sudo apt-get install ros-kinetic-map-server
$ sudo apt-get install ros-kinetic-move-base
$ sudo apt-get install ros-kinetic-amcl
```

To run PGM map creator need to install `libignition-math2-dev` and `protobuf-compiler` to compile the map creator
```bash
sudo apt-get install libignition-math2-dev protobuf-compiler
```

### Simulation Setup
To begin with simulation setup `roslaunch <YOUR PACKAGE NAME> world.launch`
```bash
$ roslaunch my_robot world.launch
```

### Map Setup
Current robot (my_robot) has zero information on its surroundings. To generate the map from Gazebo world directly using ROS package: [pgm_map_creator](https://github.com/udacity/pgm_map_creator). The map ROS AMCL Package uses is a `pgm` file.

To generate map,
```bash
# cd /home/workspace/catkin_ws/src/<YOUR PACKAGE NAME>
$ cd /home/workspace/catkin_ws/src/whereami
$ mkdir maps
$ cd /home/workspace/catkin_ws/src/
$ git clone https://github.com/udacity/pgm_map_creator.git
$ cd ..
$ catkin_make
$ cd src/my_robot/worlds
# copy current world file into world folder in pgm_map_creator package
# cp <YOUR GAZEBO WORLD FILE> src/pgm_map_creator/world/
$ cp cho_robot_world.world src/pgm_map_creator/world/

```

In `src/pgm_map_creator/world/` add the following tag towards the end of the file, but just before </world> tag into [world](https://github.com/bmaxdk/RoboticsND-where-am-i/blob/main/catkin_ws/src/pgm_map_creator/world/cho_robot_world.world) file.
```xml
<plugin filename="libcollision_map_creator.so" name="collision_map_creator"/>
```

To create the PGM map, need to run `gzserver` with the map file and launch the `request_publisher` node
```bash
# gzserver src/pgm_map_creator/world/<YOUR GAZEBO WORLD FILE>
$ gzserver src/pgm_map_creator/world/cho_robot_world.world

# open another terminal
$ roslaunch pgm_map_creator request_publisher.launch
```
If the map is cropped, you might want to adjust the parameters in `launch/request_publisher.launch`, namely the `x` and `y` values, which defines the size of the map:
```xml
  <arg name="xmin" default="-15" />
  <arg name="xmax" default="15" />
  <arg name="ymin" default="-15" />
  <arg name="ymax" default="15" />
  <arg name="scan_height" default="5" />
  <arg name="resolution" default="0.01" />
```

Once pgm map is created in maps folder, move map.map to your package. After that need to create `yaml` file providing the [metadata about the map](http://wiki.ros.org/map_server#YAML_format)
```bash
$ cd /home/workspace/catkin_ws/
# cp src/pgm_map_creator/maps/<YOUR MAP NAME>  src/<YOUR PACKAGE NAME>/maps/<YOUR MAP NAME>
$ cp src/pgm_map_creator/maps/map.pgm  src/whereami/maps/
$ cd src/whereami/maps
touch map.yaml
```
In map.yaml
```yaml
image: map.pgm
resolution: 0.01
origin: [-15.0, -15.0, 0.0]
occupied_thresh: 0.65
free_thresh: 0.196
negate: 0
```
The default map size is 30 by 30, so the origin will be [-15, -15, 0], i.e. half the size of the map.

### AMCL `launch` file
[whereami](https://github.com/bmaxdk/RoboticsND-where-am-i/tree/main/catkin_ws/src/whereami) package contains `AMCL launch` file.
The following steps applied to create AMCL launch file
```bash
# cd /home/workspace/catkin_ws/src/<YOUR PACKAGE NAME>/launch/
$ cd /home/workspace/catkin_ws/src/whereami/launch/
$ touch amcl.launch
```


## Directory Structure
```bash
workspace/catkin_ws/src# tree
.
├── ball_chaser
│   ├── CMakeLists.txt
│   ├── launch
│   │   └── ball_chaser.launch
│   ├── package.xml
│   ├── src
│   │   ├── drive_bot.cpp
│   │   └── process_image.cpp
│   └── srv
│       └── DriveToTarget.srv
├── CMakeLists.txt -> /opt/ros/kinetic/share/catkin/cmake/toplevel.cmake
├── my_robot                                            # Package contains robot and world
│   ├── CMakeLists.txt
│   ├── config
│   │   ├── base_local_planner_params.yaml
│   │   ├── costmap_common_params.yaml
│   │   ├── global_costmap_params.yaml
│   │   ├── local_costmap_params.yaml
│   │   └── __MACOSX
│   ├── launch
│   │   ├── amcl.launch
│   │   ├── robot_description.launch
│   │   └── world.launch
│   ├── maps
│   │   ├── map.pgm
│   │   └── map.yaml
│   ├── meshes
│   │   └── hokuyo.dae
│   ├── package.xml
│   ├── urdf
│   │   ├── my_robot.gazebo
│   │   └── my_robot.xacro
│   └── worlds
│       ├── cho_robot_world.world                       # Current world file: Will use it to generate pgm_map
│       └── robotl1_old.world
├── pgm_map_creator                                     # Package for converting a world into a pgm map
│   ├── CMakeLists.txt
│   ├── CODEOWNERS
│   ├── launch
│   │   └── request_publisher.launch
│   ├── LICENSE
│   ├── maps
│   │   └── map.pgm                                     # PGM map generated from pgm_map_creator
│   ├── msgs
│   │   ├── CMakeLists.txt
│   │   └── collision_map_request.proto
│   ├── package.xml
│   ├── README.md
│   ├── src
│   │   ├── collision_map_creator.cc
│   │   └── request_publisher.cc
│   └── world
│       ├── cho_robot_world.world                       # my world file with plugin added to generate pgm map
│       └── udacity_mtv
├── teleop_twist_keyboard                               # (Optioin-2) Teleop Robot package
│   ├── CHANGELOG.rst
│   ├── CMakeLists.txt
│   ├── package.xml
│   ├── README.md
│   └── teleop_twist_keyboard.py
└── whereami                                            # Package
    ├── CMakeLists.txt
    ├── config
    │   ├── base_local_planner_params.yaml
    │   ├── costmap_common_params.yaml
    │   ├── global_costmap_params.yaml
    │   ├── local_costmap_params.yaml
    │   └── __MACOSX
    ├── include
    │   └── whereami
    ├── launch
    │   └── amcl.launch
    ├── maps
    │   ├── map.pgm                                     # pgm map of wold file that created from pgm_map_creator
    │   └── map.yaml
    ├── package.xml
    └── src

```


## Control Step
#### (Option-1)Publishing Directly
Open a new terminal window anywhere in the catkin_ws and publish velocity commands directly to the robot's wheel actuators. To stop vehicle publish zero values and then Ctrl + C.
```bash
$ rostopic pub /cmd_vel geometry_msgs/Twist  "linear:
  x: 0.1
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.1" 
```

#### (Optioin-2) Teleop Robot
Open new terminal and from anywhere inside catkin_ws
```bash
$ rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```

#### (Option-3) Navigation Goal
Send a `2D Nav Goal` from RViz. Click the `2D Nav Goal` button in the toolbar, then click and drag on the map to send the goal to the robot. It will start moving and localize itself in the process. If you would like to give the amcl node a nudge, you could give the robot an initial position estimate on the map using 2D Pose Estimate.



## Tuning Localization Parameters
The amcl package has a lot of parameters to select from. Different sets of parameters contribute to different aspects of the algorithm. Broadly speaking, they can be categorized into three categories - overall filter, laser, and odometry. Let’s cover some of the parameters that we recommend you start with or details to focus on.

Overall Filter
min_particles and max_particles - As amcl dynamically adjusts its particles for every iteration, it expects a range of the number of particles as an input. Often, this range is tuned based on your system specifications. A larger range, with a high maximum might be too computationally extensive for a low-end system.
initial_pose - For the project, you should set the position to [0, 0]. Feel free to play around with the mean yaw value.
update_min* - amcl relies on incoming laser scans. Upon receiving a scan, it checks the values for update_min_a and update_min_d and compares to how far the robot has moved. Based on this comparison it decides whether or not to perform a filter update or to discard the scan data. Discarding data could result in poorer localization results, and too many frequent filter updates for a fast moving robot could also cause computational problems.
Laser
There are two different types of models to consider under this - the likelihood_field and the beam. Each of these models defines how the laser rangefinder sensor estimates the obstacles in relation to the robot.

The likelihood_field model is usually more computationally efficient and reliable for an environment such as the one you are working with. So you can focus on parameters for that particular model such as the -

laser_*_range
laser_max_beams
laser_z_hit and laser_z_rand
Tuning of these parameters will have to be experimental. While tuning them, observe the laser scan information in RViz and try to make sure that the laser scan matches or is aligned with the actual map, and how it gets updated as the robot moves. The better the estimation of where the obstacles are, the better the localization results.

Odometry
odom_model_type - Since you are working with a differential drive mobile robot, it’s best to use the diff-corrected type. There are additional parameters that are specific to this type - the odom_alphas (1 through 4). These parameters define how much noise is expected from the robot's movements/motions as it navigates inside the map.

Note: The odometry information for this project is received directly from Gazebo, and is equivalent to the ground truth value (no noise expected). So, you need not have to tune these parameters and can leave them at their default values. But feel free to experiment with some values and see if you notice any changes.

Important: The above set of parameters should help you get started, however they aren't the only ones that can improve your results. You are encouraged and required to go through the documentation, identify which parameters might help you improve your localization results, and experiment with them. All the remaining parameters and corresponding documentation can be found on the ROS wiki's amcl page.

If you received warning on `Transform Timeout` and `Map Update Loop`, you might want to configure the corresponding parameters. Namely larger transform_tolerance value for the AMCL node and lower update_frequency & publish_frequency values in the configuration files.
