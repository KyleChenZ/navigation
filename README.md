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
-------Updated (Feb 7, 2019)------
#### Must do: File modification (must do)(do this only once)
Download laser_cut.cpp and gen.py files from Girts email

Files will be download to ~/Download by default

##### laser_cut.cpp
To replace laser_cut.cpp in fastpick/src in TX2, type the following commands in a laptop terminal:
    
    cd ~/Download
    sftp nvidia@192.168.7.221
    cd ~/navigation/src/fastpick/src
    put laser_cut.cpp

After putting laser_cut.cpp, press ctrl+z to terminate sftp, then
    
    ssh nvidia@192.168.7.221
    cd navigation
    catkin_make
    
##### gen.py
To replace gen.cpp in fastpick/nodes in base raspberry pi, type the following commands in a laptop terminal:
    
    cd ~/Download
    sftp ubuntu@192.168.7.210
    cd ~/catkin_ws/src/fastpick/nodes
    put gen.py
    
------End of Update (Feb 7, 2019)-------

#### Note
Connect to Sake Robot's Wifi

To access to TX2, type the following command in a laptop terminal:
    
    ssh nvidia@192.168.7.221
To access to robot base (raspberry pi), type the following command in a new laptop terminal :
    
    ssh ubuntu@192.168.7.210

#### RPLidar Debug
The RPLidar should be rotating on startup. If not, make sure the power cable is connected and powered. 

Then, check if the RPLidar is connect to ttyUSB0 of TX2 by typing the following command in a TX2 terminal:
    
    ls /dev/ttyUSB*

If the result is /dev/ttyUSB0, then type the following command in a TX2 terminal to start the RPLidar motor:
    
    systemctl restart rplidar.service

Else if the result is /dev/ttyUSB1, unplug the RPLidar data cable and plug again. Now it should be /dev/ttyUSB0. Then run above the command. 

#### Note
- Press shift+ctrl+t in a terminal to open a new tab. which is more organized and cleaner compared to openning a new window.

Mapping
---
### TX2
To start making map using Google Cartographer SLAM, type the following command in a TX2 terminal:

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

5. Once you can access to TX2's folder, open the map.pgm file to see if all necessary walls (cardboards in the arena) are appeared. If not, keep the robot moving and building map. Then repeat 1-5 steps.

### End of mapping
Now you can end all the programs.

Localization & Setting Waypoints & Navigation
---
### Localization:
#### TX2:
To start localize, type the following command in a TX2 terminal:

    roslaunch go navigation.launch

#### Laptop:
To view map and localize the robot, type the following command in a laptop terminal:

    rosrun rviz rviz

In rviz, provide the robot an initial pose by pressing 2D Pose Estimate

After pressing the 2D Pose Estimate button, move your cursor to the robot location on the map. Then left click and hold and move your cursor and release to give a estimated direction (angle).

Now laser scan (red particle) should match the wall/obstables. If not, then do 2D Pose Estimate more times to get better initial pose. 

##### Note:
To make the robot localized itself precisely before navigating, type the following command in a laptop terminal.

    rosservice call /request_nomotion_update

You can run this command as many times as you want to localize it manually.

### Setting Waypoints (automatically generated) (Girts)
-------Updated (Feb 7, 2019)------
#### raspberry pi (base)：
run gen.py：
    
    rosrun fastpick gen.py

delete records

then set four waypoint in counterclockwise and set dump position

save file

Now waypoints are set and the gen.py terminal should show something like:
    
    scan area corners:
    <rosparam param="scan_area">[ [3.123,-2.557], [-0.294,-2.704], [-0.537,2.648], [2.867,2.762] ]</rosparam>

Now copy that <rosparam ... /rosparam> line and it will be used to configure a file on TX2 for scan_area

#### TX2:
Use your preferred text editor to insert a line in ~/navigation/src/fastpick/launch/depth-cam.launch file
    
    roscd fastpick/launch
    vim depth-cam.launch

Go to the line before <node name="laser_cut" pkg="fastpick" type="laser_cut"/> and add the line that you copied from gen.py of raspberry pi (base)

Now waypoints and scan area are set up. 
    

### Navigation (Girts):
#### TX2:
To start camera node and laser cut for localization and navigation:
    
    roslaunch go navigation_with_laser_cut.launch

#### laptop:
Open rviz and make sure robot is localized

------End of Update (Feb 7, 2019)-------

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
