# Homework-2---Gazebo-ROS

## **Install ros melodic and update**


```````

~$ sudo apt install ros-melodic-desktop-full
~$ sudo rm /etc/ros/rosdep/sources.list.d/20-default.list
~$ sudo rosdep init
~$ rosdep update

```````

## **Install gazebo**


```````

~/catkin_ws$ sudo apt install ros-melodic-gazebo-ros

```````

## **Create workspace**


```````

~$ cd ~/catkin_ws/
~$ catkin_make

```````

## **Open the link in Gitlab to be able to clone the repository**

https://gitlab.com/beta-robots/diff_drive_sim_robot


## **Copy the folder "diff_drive_sim_robot" that is in github directory in our workspace in src**


```````

~/catkin_ws$ cd src
~/catkin_ws/src$ git clone https://gitlab.com/beta-robots/diff_drive_sim_robot.git

```````


## **Modify robot in gazebo**

First, we need to copy the file "ddsr.xacro" in the same directory "catkin_ws/src/diff_drive_sim_robot/ddsr_description/urdf" and change the name of this copy to "ddsr_partial.xacro" to do our tests without modifying the original file:


```````

~/catkin_ws/src/diff_drive_sim_robot/ddsr_description$ ll
total 28
drwxr-xr-x 5 gengiro gengiro 4096 nov 15 17:10 ./

drwxr-xr-x 6 gengiro gengiro 4096 nov 15 17:10 ../
-rw-r--r-- 1 gengiro gengiro  113 nov 15 17:10 CMakeLists.txt
drwxr-xr-x 2 gengiro gengiro 4096 nov 15 17:39 launch/
-rw-r--r-- 1 gengiro gengiro  442 nov 15 17:10 package.xml
drwxr-xr-x 2 gengiro gengiro 4096 nov 15 20:33 rviz/
drwxr-xr-x 2 gengiro gengiro 4096 nov 15 20:33 urdf/

~/catkin_ws/src$ cd diff_drive_sim_robot/
~/catkin_ws/src/diff_drive_sim_robot$ cd ddsr_description/
~/catkin_ws/src/diff_drive_sim_robot/ddsr_description$ cd urdf/

~/catkin_ws/src/diff_drive_sim_robot/ddsr_description/urdf$ cp ddsr.xacro ddsr_partial.xacro

~/catkin_ws/src/diff_drive_sim_robot$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   ddsr_description/launch/description.launch

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	ddsr_description/urdf/ddsr_partial.xacro

no changes added to commit (use "git add" and/or "git commit -a")

```````

As we can see, inside this folder "urdf" we have already copied the file where is our "ddsr_partial.xacro". Inside this file, we can edit: Name of the "robot", color, mass, etc. For that, we should open the file and edit with the following line:


```````

~/catkin_ws/src/diff_drive_sim_robot/ddsr_description/urdf$ gedit ddsr_partial.xacro 


```````

<!-- Robot name -->
<robot name="ddsr" xmlns:xacro="http://ros.org/wiki/xacro">
<xacro:arg name="namespace" default="ddsr" />

<!-- user input dimensions  ( DEBUG: all mass values have been divided by 100 )  -->
<xacro:property name="robot" value="ddsr" />
<xacro:property name="base_length" value="0.8" />
<xacro:property name="base_width" value="0.6" />
<xacro:property name="base_height" value="0.2" />
<xacro:property name="base_mass" value="300.0" />
<xacro:property name="floor_clearance" value="0.08" />
<xacro:property name="wheel_baseline" value="0.6" />
<xacro:property name="wheel_width" value="0.03" />
<xacro:property name="wheel_radius" value="0.1" />
<xacro:property name="wheel_mass" value="5.0" />
<xacro:property name="cwheel_width" value="0.02" />
<xacro:property name="cwheel_radius" value="0.05" />
<xacro:property name="cwheel_mass" value="1" />

<!-- materials -->
<material name="ddsr/blue">
	<color rgba="0 0 0.8 1"/>
</material>
<material name="ddsr/red">
	<color rgba="1 0.1 0.0 1"/>
</material>
<material name="ddsr/grey">
	<color rgba="0.1 0.1 0.1 1"/>
</material>
<material name="ddsr/white">
	<color rgba="1 1 1 1"/>
</material>


<!-- Extra definitions for gazebo simulation  -->
<xacro:include filename="$(find ddsr_gazebo)/gazebo/ddsr.gazebo" />
<xacro:gazebo_include gazebo_namespace="$(arg namespace)" />


Also, we can edit every component of the robot (local origin in rpy, mass, name, etc.) for example the platform:



<!-- ~~~~~~~~~~~~~~~~~~~~~~~ PLATFORM ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->

<link name="footprint">
	<visual>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<box size="0.01 0.01 0.01"/>
		</geometry>
		<material name="ddsr/white"/>
	</visual>
</link>

<!-- Base Link. STL is scaled with te ratio between NEW/OLD dimensions -->
<joint name="$footprint_2_platform" type="fixed" >
	<parent link="footprint"/>
	<child link="platform"/>
	<origin xyz="${base_length*0.25} 0 ${floor_clearance+base_height*0.5}" rpy="0 0 0"/>
</joint>
<link name="platform">
	<visual>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<box size="${base_length} ${base_width} ${base_height}"/>
		</geometry>
		<material name="ddsr/blue" />
	</visual>
	<collision>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<box size="${base_length} ${base_width} ${base_height}"/>
		</geometry>
	</collision>
	<inertial>
		<mass value="${base_mass}" />
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<inertia ixx="${0.083*base_mass*(base_width*base_width+base_height*base_height)}"
			ixy="0.0"
			ixz="0.0"
			iyy="${0.083*base_mass*(base_length*base_length+base_height*base_height)}"
			iyz="0.0"
			izz="${0.083*base_mass*(base_length*base_length+base_width*base_width)}" />
	</inertial>
</link>

In the other components we can also edit the same items and link them with the platform as a base in "<parent link="platform"/>":

```````

<!-- ~~~~~~~~~~~~~~~~~~~~~~~ DRIVE WHEELS ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ -->

<joint name="platform_2_left_wheel" type="continuous" >
	<parent link="platform"/>
	<child link="left_wheel"/>
	<origin xyz="${-base_length*0.3} ${base_width*0.5} ${-base_height*0.5+wheel_radius-floor_clearance}" rpy="${-0.5*pi} 0 0"/>
	<axis xyz="0 0 1"/>
	<limit effort="99999" velocity="${20*pi}"/>
	<dynamics friction="0.25" damping="0.05"/>
</joint>
<link name="left_wheel">
	<visual>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<cylinder length="${wheel_width}" radius="${wheel_radius}"/>
		</geometry>
		<material name="ddsr/grey" />
	</visual>
	<collision>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<cylinder length="${wheel_width}" radius="${wheel_radius}"/>
		</geometry>
	</collision>
	<inertial>
		<mass value="${wheel_mass}" />
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<inertia ixx="${0.083*wheel_mass*(3*wheel_radius*wheel_radius+wheel_width*wheel_width)}"
			ixy="0.0"
			ixz="0.0"
			iyy="${0.083*wheel_mass*(3*wheel_radius*wheel_radius+wheel_width*wheel_width)}"
			iyz="0.0"
			izz="${0.5*wheel_mass*wheel_radius*wheel_radius}" />
	</inertial>
</link>

<joint name="platform_2_right_wheel" type="continuous" >
	<parent link="platform"/>
	<child link="right_wheel"/>
	<origin xyz="${-base_length*0.3} ${-base_width*0.5} ${-base_height*0.5+wheel_radius-floor_clearance}" rpy="${-0.5*pi} 0 0"/>
	<axis xyz="0 0 1"/>
	<limit effort="99999" velocity="${20*pi}"/>
	<dynamics friction="0.25" damping="0.05"/>
</joint>
<link name="right_wheel">
	<visual>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<cylinder length="${wheel_width}" radius="${wheel_radius}"/>
		</geometry>
		<material name="ddsr/grey" />
	</visual>
	<collision>
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<geometry>
			<cylinder length="${wheel_width}" radius="${wheel_radius}"/>
		</geometry>
	</collision>
	<inertial>
		<mass value="${wheel_mass}" />
		<origin xyz="0 0 0" rpy="0 0 0"/>
		<inertia ixx="${0.083*wheel_mass*(3*wheel_radius*wheel_radius+wheel_width*wheel_width)}"
			ixy="0.0"
			ixz="0.0"
			iyy="${0.083*wheel_mass*(3*wheel_radius*wheel_radius+wheel_width*wheel_width)}"
			iyz="0.0"
			izz="${0.5*wheel_mass*wheel_radius*wheel_radius}" />
	</inertial>
</link>

<transmission name="$platform_2_left_wheel_transmission">
	<type>transmission_interface/SimpleTransmission</type>
	<joint name="platform_2_left_wheel">
		<hardwareInterface>hardware_interface/VelocityJointInterface</hardwareInterface>
	</joint>
	<actuator name="left_wheel_motor">
		<hardwareInterface>EffortJointInterface</hardwareInterface>
		<mechanicalReduction>30</mechanicalReduction>
	</actuator>
</transmission>
<transmission name="$platform_2_right_wheel_transmission">
	<type>transmission_interface/SimpleTransmission</type>
	<joint name="platform_2_right_wheel">
		<hardwareInterface>hardware_interface/VelocityJointInterface</hardwareInterface>
	</joint>
	<actuator name="right_wheel_motor">
		<hardwareInterface>EffortJointInterface</hardwareInterface>
		<mechanicalReduction>30</mechanicalReduction>
	</actuator>
</transmission>


~/catkin_ws/src/diff_drive_sim_robot/ddsr_description/urdf$ ll
total 32
drwxr-xr-x 2 gengiro gengiro  4096 nov 15 20:33 ./
drwxr-xr-x 5 gengiro gengiro  4096 nov 15 17:10 ../
-rw-r--r-- 1 gengiro gengiro 10370 nov 15 17:10 ddsr_partial.xacro
-rw-r--r-- 1 gengiro gengiro 10367 nov 15 20:33 ddsr.xacro

```````

If we open this folder, we can modify

```````

~/catkin_ws/src/diff_drive_sim_robot/ddsr_description/urdf$ cd ..
~/catkin_ws/src/diff_drive_sim_robot/ddsr_description$ cd launch/
~/catkin_ws/src/diff_drive_sim_robot/ddsr_description/launch$ ll
total 12
drwxr-xr-x 2 gengiro gengiro 4096 nov 15 17:39 ./
drwxr-xr-x 5 gengiro gengiro 4096 nov 15 17:10 ../
-rw-r--r-- 1 gengiro gengiro 1444 nov 15 17:39 description.launch


~/catkin_ws/src/diff_drive_sim_robot/ddsr_description/launch$ nano description.launch 

```````

<?xml version="1.0"?>


<launch>

        <!-- Input arguments -->
        <arg name="namespace" default="ddsr" />
        <arg name="run_joint_state_publisher" default="true" doc="Run joint state publisher"/>
        <arg name="joint_state_gui"  default="true" doc="Enable joint state publisher gui"/>
        <arg name="rviz"  default="true" doc="Enable/disable visualization"/>

        <!-- Convert xacro to urdf and set robot_description parameter in the parameter server -->
        <param  name="$(arg namespace)/robot_description"
                command="$(find xacro)/xacro $(find ddsr_description)/urdf/ddsr_partial.xacro" />

        <!-- Run joint state publisher (Gets robot_description from parameter server)-->
        <group if="$(arg run_joint_state_publisher)">
                <node
                        ns= "$(arg namespace)"
                        pkg="joint_state_publisher"
                        type="joint_state_publisher"
                        name="joint_state_publisher" >
                        <param name="rate" value="50" />
                        <param name="use_gui" value="true" />
                        <rosparam param="source_list">[]</rosparam>
                </node>
        </group>

        <!-- Run state publisher (Gets robot_description from parameter server)-->
        <node
                ns= "$(arg namespace)"
                pkg="robot_state_publisher"
                type="robot_state_publisher"
                name="robot_state_publisher" >
                <param name="publish_frequency" value="50" />
        </node>

        <!-- Run rviz node -->
        <group if="$(arg rviz)">
                <node
                        pkg="rviz"
                        type="rviz"
                        name="model_rviz"
                        args="-d $(find ddsr_description)/rviz/ddsr_description.rviz">
                </node>
        </group>

</launch>



## **Launch gazebo**

After this modifications, we launch Gazebo:

```````

~/catkin_ws$ roslaunch ddsr_description description.launch 

```````
