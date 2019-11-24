cmake_minimum_required(VERSION 2.8.3)
project(ddsr_gazebo)

find_package(catkin REQUIRED COMPONENTS
	ddsr_description
	gazebo_ros
)

catkin_package(
	CATKIN_DEPENDS
	ddsr_description
	gazebo_ros
)
