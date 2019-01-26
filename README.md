SAKE Robot Mapping & Navigation Instruction
====================
Download this repository to your laptop ros workspace in src (e.g., catkinws/src/):
---
- git clone -b kinetic-devel https://github.com/KyleChenZ/navigation.git

Hareware setup
---
- Make sure RPLidar's power cable and data cable are connected.

Software setup
---
### Laptop
- Connect to Sake Robot's Wifi
- To access to TX2, type the following command in a laptop terminal:
    
    ssh nvidia@192.168.7.221
- To access to robot base (raspberry pi), type the following command in a new laptop terminal :
    
    ssh ubuntu@192.168.7.210

#### Note
- Press shift+ctrl+t in a terminal to open a new tab. which is more organized and cleaner compared to openning a new window.

Mapping
---
### TX2
To make lidar rotating and start making map using Google Cartographer SLAM, type the following command in a TX2 terminal:

    roslaunch go mapping.launch
### Laptop
To view the map being generated, type the following command in a laptop terminal:

    rosrun rviz rviz

To control the robot movement with keboard teleoperation:
    
    rosrun teleop_twist_keyboard teleop_twist_keyboard.py

Or you can use a joystick instead of a laptop keyboard.

Now move the robot around and build the map.

Meanwhile, look at the map in rviz to see if all necessary walls (cardboards in the arena) are appeared.

### TX2
If the map in rviz is satisfying and you want to save it.

To save the map, type the following command in a TX2 terminal:
    
    rosrun map_server map_saver -f ~/map

### Laptop
To verify the map saved before closing/ending the mapping program:
1. open home folder
2. press ctrl+L
3. enter 

    sftp://nvidia@192.168.7.221/home/nvidia/

4. a new window may pop up and ask for passphrase and password:
- passphrase can be empty
- password use TX2‘s password
5. Once you can access to TX2's folder, open the map.pgm file to see if all necessary walls (cardboards in the arena) are appeared.

### End of mapping
Now you can end all the programs.

Localization & Setting Waypoints & Navigation
---
### Localization:
#### TX2:
To rotate the lidar and start localize, type the following command in a TX2 terminal:

    roslaunch go navigation.launch

#### Laptop:
To view map and localize the robot, type the following command in a laptop terminal:

    rosrun rviz rviz

In rviz, provide the robot an initial pose by pressing 2D Pose Estimate

After pressing the 2D Pose Estimate button, move your cursor to the robot location on the map. Then left click and hold and move your cursor and release to give a estimated direction (angle).

Now laser scan (red particle) should match the wall/obstables. If not, then do 2D Pose Estimate more times to get better initial pose. 

##### optional:
To make the robot localized itself precisely before navigating, type the following command in a laptop terminal.

    rosservice call /request_nomotion_update

You can run this command as many times as you want to localize it manually.

### Setting Waypoints (manually) (ignore this step if waypoints can be automatically generated):
#### TX2:
To start setting waypoint service:
    
    rosrun go generate.py

#### Laptop:
Use joystick or keyboard to move the robot to a desired location.
And set waypoints.

To delete/clean the original waypoints:

    rosservice call /new

To set a waypoint with name:

    rosservice call /setpoint name
    
Note that the "name" can be change to anything like "slot1", "location1", etc..

To set the waypoints:

    rosservice call /save
    
#### TX2:
After saving the waypoints.
You can make your own routine by editing the sort.yaml file:

    nano ~/navigation/src/go/param/sort.yaml

### Navigation (Girts):
#### raspberry pi (base):
To set up (calibrate) the arm, run the following commands in a Base terminal:

    ~/home-x.py
    ~/home-y.py
    ~/calibrate-gripper.py

To enable arm picking service, type the following command in a Base terminal:

    rosrun fastpick pick.py

To start navigating:
    
    rosrun fastpick drive.py
    
    
-------------End of the Instruction----------------------

About this fork:
====================
- This fork modified map_saver.cpp to get better saved map for Google Cartographer

Original README from fork:
====================

ROS Navigation Stack
---

A 2D navigation stack that takes in information from odometry, sensor
streams, and a goal pose and outputs safe velocity commands that are sent
to a mobile base.

 * AMD64 Debian Job Status: [![Build Status](http://build.ros.org/buildStatus/icon?job=Kbin_uX64__navigation__ubuntu_xenial_amd64__binary)](http://build.ros.org/view/Kbin_uX64/job/Kbin_uX64__navigation__ubuntu_xenial_amd64__binary/)

Related stacks:

 * http://github.com/ros-planning/navigation_msgs (new in Jade+)
 * http://github.com/ros-planning/navigation_tutorials
 * http://github.com/ros-planning/navigation_experimental

For discussion, please check out the
https://groups.google.com/group/ros-sig-navigation mailing list.
