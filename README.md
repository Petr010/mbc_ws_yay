# mbc_ws_yay
Rutgers ECE SP24 team 46, MINI BUSCH CAR


For Ubuntu 22.04

Get Python 3, Arduino IDE, ROS2-hu, NAV2
  python 3: https://phoenixnap.com/kb/how-to-install-python-3-ubuntu
  arduino ide - from ubuntu store
  ROS2: https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html
  NAV2: https://navigation.ros.org/development_guides/build_docs/index.html 

Navigate to src -> Ros_Arduino_bridge and load to arduino (change motor pins if needed in motor_driver.h)
  (reference) https://github.com/joshnewans/ros_arduino_bridge
  Launch arduino IDE and locate where arduino pops up exp) /dev/USB0 *note, make sure ch340 drivers are installed
  Uninstall britty first: https://www.reddit.com/r/pop_os/comments/uf54bi/comment/i6rifto/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button
  https://www.youtube.com/watch?v=Y2mvdlnqRzA (use sudo dmesg if no permission)
  Flash arduino with ROSArduinoBride.ino

Find file carlikebot.ros2_control.xacro (or similar name) Then in <param name="device">... change /dev/serial… to your device port, 
exp) <param name="device">/dev/ttyUSB1</param>

Launch: In terminal under, run: (will take a while)
  cd mbc_ws_yay
  sudo apt install libserial-dev
  colcon build --symlink-install 
  source install/setup.bash
  ros2 launch ros2_control_demo_example_11 carlikebot.launch.py

Controller: In another terminal run:
  cd mbc_ws_yay
  source install/setup.bash
  ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/bicycle_steering_controller/reference_unstamped

Navigation: In another terminal run: 
  cd mbc_ws_yay
  source install/setup.bash
  ros2 launch ros2_control_demo_example_11 navigation_launch.py use_sim_time:=false


In VScode or a similar editor, it might be necessary to find all the files which contains the path: petr/home/ 
change this to the appropriate pathes


If you want to build your own packages similar to how we did, this is what we downloaded and modified to the point of navigation along with some rough notes:

1.	Python 3
  a.	https://phoenixnap.com/kb/how-to-install-python-3-ubuntu
2.	arduino IDE
  a.	(UBUNTU SOFTWARE STORE)
3.	ROS2 and NAV2
  a.	ROS2: https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html
  b.	NAV2: https://navigation.ros.org/development_guides/build_docs/index.html 
4.	LiDar Mapper:
  a.	In Terminal Run: mkdir -p ~/ros2_ws/src
  b.	https://github.com/Myzhar/ldrobot-lidar-ros2/tree/main
5.	Download Ros_Arduino_bridge and load to arduino (change motor pins if needed in motor_driver.h)
  a.	https://github.com/joshnewans/ros_arduino_bridge
  b.	Extract to ros2_ws
6.	Download Diffbot_controller example (do not git clone, download zip, you want humble branch)
  a.	https://github.com/joshnewans/diffdrive_arduino/tree/humble
  b.	Extract to ros2_ws/src
  c.	Launch arduino IDE and locate where arduino pops up exp) /dev/USB0 *note, make sure ch340 drivers are installed
    i.	Uninstall britty first: https://www.reddit.com/r/pop_os/comments/uf54bi/comment/i6rifto/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button
    ii.	https://www.youtube.com/watch?v=Y2mvdlnqRzA (use sudo dmesg if no permission)
  d.	Find file diffbot.ros2_control.xacro in ros2_ws/src/diffdrive_arduino-humble/description/ros2_control
      Then in <param name="device">... change /dev/serial… to your device port, 
    i.	exp) <param name="device">/dev/ttyUSB1</param>
  e.	Flash arduino with ROSArduinoBride.ino
  f.	Launch: In terminal under, run:
    i.	cd ros2_ws
    ii.	sudo apt install libserial-dev
    iii.	colcon build --symlink-install
    iv.	source install/setup.bash
    v.	ros2 launch diffdrive_arduino diffbot.launch.py
   g.	Controller: In another terminal run:
    i.	cd ros2_ws
    ii.	ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=diffbot_base_controller/cmd_vel_unstamped


(the madness begins, before this point, you can drive a diffbot with your keyboard)


IT WORKKKS

ros2 launch ros2_control_demo_example_11 carlikebot.launch.py

ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/bicycle_steering_controller/reference_unstamped




Learning navs:

ros2 run twist_mux twist_mux --ros-args --params-file ./src/articubot_one-humble/config/twist_mux.yaml -r cmd_vel_out:=diff_cont/cmd_vel_unstamped



SO FAR:
Edited example 11 with https://www.youtube.com/watch?v=J02jEKawE5U to get it to work with arduino over serial.  Then disabled rviz and ran controller with reference_unstamped.

Incorporated twist_mux so can just pull up teleop standard and it will automap to proper controller

Incorporate nav2 using: https://www.youtube.com/watch?v=jkoGkAd0GYk changed base_link to ldlidar_base in the nav2 params file so it can communicate with the model made in rviz.

Used the plugins for ackerman steering laid out for the nav2 implementation of this repo: https://github.com/rshorton/elsabot_jeep/tree/main.  Copied param file as their car closely matches ours

After creating a map with slam, we called the lidar_rviz2 launch file within the robot_launch file.

Changed nav file to https://github.com/rshorton/elsabot_jeep/tree/main to better match dimensions of car

Adjusting params for optimal driving and attempting to load map

Incorporate straightening function into arduino

