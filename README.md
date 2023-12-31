# Digital-Twins-for-ROV
This project involves the creation of a Digital Twin (DT) system for underwater ROV teleoperation. The DT system offers functionalities for teleoperation, simulation and autonomous navigation. The entire project was done in simulation. The project tries to solve dynamic environment representation, spatial awareness and workload issues involved in underwater ROV teleoperation by building a DT that is also an underwater teleoperation interface.

# System Overview
![DT twin system overview](./Picture1.png)

# System Environment
The system was built on the UWRobotics simulator (DT digital component) and ROS (DT physical component). For the UWRobotics simulator Unity version 2019.4.4f1 was used, while for the ROS system ROS melodic running on Ubuntu 18.04 was used. 

# Packages to install
The following listed packages are needed for the full working system and testing.

**1.** UWRobotics Simulator - UWRobotics simulator is a unity based underwater robotics simulator. The simulator is used for visualisation, simulation and control purposes. Installation of the simulators unity and ROS packages are found [here](https://github.com/org-arl/UWRoboticsSimulator)

**2.** ORB_u - An ORB-SLAM3 implementation for underwater robots. Available [here](https://github.com/discoimp/orb_u)

**3.** Bluerov2 - A package for bluerov2 integration with ROS and UUV simulator. Available [here](https://github.com/FletcherFT/bluerov2). The imc_ros_bridge dependency package for might need to be installed from source.

**4.** UUV simulator - A gazebo underwater simulator. Available [here](https://uuvsimulator.github.io/installation/).

The dt-ros directory of this repository represents the ROS packages created for this project and should be downloaded and integrated with a ROS system. The 'Original_folder' directory represents script in the UWRobotics codebase that have been modified for the DT system to work. The other subdirectories represent unity folders and scripts, these should be cloned into a newly created directory in the UWRobotics simulator codebase. For example you can create a folder with the name "!" and clone this repo into it. Then move the dt-ros system to a ros component of your setup.<br>
For building the ROS packages use the ```catkin build``` command.


# System testing and workings
The individual components of the system were built and tested seperately. First to connect the DT component and the ROS system, the steps are illustrated in the simulators [readme](https://github.com/org-arl/UWRoboticsSimulator/blob/main/README.md). In this package, the UW robotics starts screen has been duplicated, edited and should be run instead.

**1.** For the dynamic representation of the environment : The system was tested on real world [data](https://seafile.lirmm.fr/d/79b03788f29148ca84e5/?p=%2FHarbor_sites_sequences&mode=list) using the already purposed ORB_u SLAM. For this the harbor rosbag dataset 1,3,5 and 6 were tested. Here the point clouds from the ORB-SLAM are interpolated over for a dense representation of the sea floor environment and these points and the robots orientation are transferred over the communication channel for the DT digital component representation. The rosbag play command should be replaced with the actual path to the downloaded dataset. To change view point from the DT Unity environment, keypress "1","2" and "3" for different views.
<br>To run <br>
&emsp;&emsp;- Connect the unity and ROS systems<br>
&emsp;&emsp;- On the ros system run the orb_u slam node, the densify node and the harbour dataset with the following respective commands and order.<br>
```
        roslaunch orb_slam3_ros aqualoc_harbor.launch
```
```
        rosrun dt_ros densify.py
```
```
        rosbag play "path_to_harbour_dataset".bag
```

![sample-video-dynamic-representation](./videos/ezgif.com-video-to-gif.gif)

![sample-video-dynamic-representation](./videos/ezgif.com-video-to-gif(1).gif)

**2.** For autonomous navigation : Following the transversal of the robot keypress "M" saves the ORB-SLAM map and connects to the path planner to plan and publish a path. For this project only a path planner was implemented in the autonomous system of the DT. 
<br>To run <br>
&emsp;&emsp;- Follow the above steps in (1), except the last step<br>
&emsp;&emsp;- On the ros system run the ocotmap and pathplanning node with the following command.<br>
```
        rosrun dt_ros octo
```
```
        rosrun dt_ros pathplanning
```
&emsp;&emsp;- Complete the process by executing the last command in (1) i.e play the Rosbag file<br>

The following images showcase octomap representation of mapped seafloors and planned paths visualization.
<br><br>
<img src="./octo_0.1res.png" width="500">
<br>
<img src="./octo+planned_path.png" width="500">
<br>

**3.** For teleoperation : To test the teleoperation feature of the DT we used an underwater simulator (UUV simulator) to replace a real robot in an underwater environment.
<br>To run <br>
&emsp;&emsp;- Connect the unity and ROS system<br>
&emsp;&emsp;- On the ros system run the UUV simulator and its various peripherals with the following commands.<br>
```
        roscore
```
```
        roslaunch uuv_gazebo_worlds herkules_ship_wreck.launch gui:=true paused:=true
```
```
        roslaunch bluerov2_description upload.launch z:=-50 x:=-15
```
```
        roslaunch bluerov2_control start_thruster_manager.launch
```

&emsp;&emsp;- For a more detailed instruction on how to setup the UUV simulator refer to [this](https://github.com/FletcherFT/bluerov2/blob/master/TUTORIAL-01.md)<br>

The keymappings for teleoperation are shown in the table below;<br>

| Key     | Function     |  
| ------------- | ------------- | 
| Right Arrow         | Torque, y axis         | 
| Left Arrow         | Torque, x axis        | 
| Up Arrow          | Torque, z axis         | 
| A         | Force, x axis        | 
| D         | Force, y axis        | 
| W          | Force, z axis        | 
| Q         | Increase Speed         | 
| E         | Decrease Speed         | 

![sample-video-teleoperation](./videos/ezgif.com-video-to-gif(2).gif)

**4.** For simulation : The UW robotics simulator teleoperation simulation has been integrated into the system .
<br>To run <br>
&emsp;&emsp;- Connect the unity and ROS system<br>
&emsp;&emsp;- On the ros system run the UW robotics ROS package to control the robot.<br>
```
        rosrun controllerpkg key_publisher.py
```

<br>
<img src="./simulation_interface_rov.png" width="500">
<br>

![sample-video-simulation](./videos/ezgif.com-video-to-gif(3).gif)

**Note:** For some system testing, the ros bridge subscriber files topics in unity might need uncommenting in some sections. This is the case for the camera, cloud and pose subscriber where testing different system component required different topics or message types. For the path planning , the start state might be invalid due to the map resolution and implemented collision geometry. For the unity system, the DT, start_DT, test_DT and simulation scenes should be added to "scenes in build" under "file/build settings" and rebuilt. Preferably their numbering should be 3,4, 5 and 6 so as not to change their numbers in respective places in the codebase.  <br><br>

**Installation Issues:** For installation of ORB-SLAM_u, the open cv version will have to be changed in the cmakelist when using ubuntu 18.04.

## References

Reference materials are enumerated below;

[1]	G. Ellingsen, “ORB-SLAM3-Underwater.” 02/20. [Online]. Available: https://github.com/discoimp/orb_u

[2]	F. Maxime, “Aqualoc Deepsea.” [Online]. Available: https://seafile.lirmm.fr/d/79b03788f29148ca84e5/?p=%2FHarbor_sites_sequences&mode=list

[3] FletcherFT, “bluerov2.” [Online]. Available: https://github.com/FletcherFT/bluerov2/tree/master

[4] G. Ayush, ‘path_planning’. [Online]. Available: https://github.com/ayushgaud/path_planning/blob/master/src/old_path_planning.cpp

[5] A. Chaudhary, R. Mishra, B. Kalyan, and M. Chitre, “Development of an Underwater Simulator using Unity3D and Robot Operating System,” in OCEANS 2021: San Diego – Porto, San Diego, CA, USA: IEEE, Sep. 2021, pp. 1–7. doi: 10.23919/OCEANS44145.2021.9706012.

[6] “flexible-collision-library.” [Online]. Available: https://github.com/flexible-collision-library/fcl

[7] M. M. M. Manhaes, S. A. Scherer, M. Voss, L. R. Douat, and T. Rauschenbach, “UUV Simulator: A Gazebo-based package for underwater intervention and multi-robot simulation,” in OCEANS 2016 MTS/IEEE Monterey, Monterey, CA, USA: IEEE, Sep. 2016, pp. 1–8. doi: 10.1109/OCEANS.2016.7761080.

[8] ros-planning, ‘moveit’. [Online]. Available: https://github.com/ros-planning/moveit/blob/master/moveit_core/CMakeLists.txt

The bluerov2 3D model (dae) file was gotten from  - https://github.com/UUVControl/bluerov2/tree/thrusterManagement/bluerov2_description/meshes

[9] F. Bonin-Font, M. Massot Campos, and A. B. Burguera, “ARSEA: A Virtual Reality Subsea Exploration Assistant,” IFAC-PapersOnLine, vol. 51, no. 29, pp. 26–31, 2018, doi: 10.1016/j.ifacol.2018.09.464.

## To do
Implement a better densification technique or use a different visual SLAM approach.<br>
Implement a more robust path planner<br>
Creation of UI elements in DT system to set path planning goal.<br>
Creation of motion planning system
