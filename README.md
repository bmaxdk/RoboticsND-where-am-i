# RoboticsND-where-am-i
## Project 3: Where Am I

Application of using the adaptive monte carlo localizatin (AMCL) algorithm is implemented in ROS. AMCL dynamically adjusts the number of particles over a period of time, as the robot navigates around in a map. The process of AMCL algorithm is followed by the [amcl](http://wiki.ros.org/amcl) ROS package to get a working localization.

[image1]: project_img/img1.png "img1"
[image2]: project_img/img2.png "img2"

![alt text][image1]

![alt text][image2]


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
├── my_robot
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
│       ├── cho_robot_world.world
│       └── robotl1_old.world
├── pgm_map_creator
│   ├── CMakeLists.txt
│   ├── CODEOWNERS
│   ├── launch
│   │   └── request_publisher.launch
│   ├── LICENSE
│   ├── maps
│   │   └── map.pgm
│   ├── msgs
│   │   ├── CMakeLists.txt
│   │   └── collision_map_request.proto
│   ├── package.xml
│   ├── README.md
│   ├── src
│   │   ├── collision_map_creator.cc
│   │   └── request_publisher.cc
│   └── world
│       ├── cho_robot_world.world
│       └── udacity_mtv
├── teleop_twist_keyboard
│   ├── CHANGELOG.rst
│   ├── CMakeLists.txt
│   ├── package.xml
│   ├── README.md
│   └── teleop_twist_keyboard.py
└── whereami
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
    │   ├── map.pgm
    │   └── map.yaml
    ├── package.xml
    └── src

```
