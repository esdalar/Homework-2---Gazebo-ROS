cmake_minimum_required(VERSION 2.8.3)

project(ddsr_control)

find_package(catkin REQUIRED COMPONENTS
	controller_manager
	ddsr_gazebo
	ddsr_description
)

catkin_package(
	CATKIN_DEPENDS
	controller_manager
	ddsr_gazebo
	ddsr_description
)
