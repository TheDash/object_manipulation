# Catkin User Guide: http://www.ros.org/doc/groovy/api/catkin/html/user_guide/user_guide.html
# Catkin CMake Standard: http://www.ros.org/doc/groovy/api/catkin/html/user_guide/standards.html
cmake_minimum_required(VERSION 2.8.3)
project(object_manipulator)
# Load catkin and all dependencies required for this package
# TODO: remove all from COMPONENTS that are not catkin packages.
find_package(catkin REQUIRED COMPONENTS roscpp rospy tf pluginlib std_srvs actionlib object_manipulation_msgs sensor_msgs pr2_controllers_msgs geometry_msgs  visualization_msgs pr2_mechanism_msgs eigen_conversions object_manipulator)

# include_directories(include ${Boost_INCLUDE_DIR} ${catkin_INCLUDE_DIRS}) 
# CATKIN_MIGRATION: removed during catkin migration
# cmake_minimum_required(VERSION 2.4.6)

# CATKIN_MIGRATION: removed during catkin migration
# include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
set(ROS_BUILD_TYPE RelWithDebInfo)

# CATKIN_MIGRATION: removed during catkin migration
# rosbuild_init()
#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#uncomment if you have defined messages
#add_message_files(

#common commands for building c++ executables and libraries
#add_library(${PROJECT_NAME} src/example.cpp)
#target_link_libraries(${PROJECT_NAME} another_library)
#
# CATKIN_MIGRATION: removed during catkin migration
# rosbuild_add_boost_directories()
#find_package(Boost REQUIRED COMPONENTS thread)
include_directories(${Boost_INCLUDE_DIRS})
#add_executable(example examples/example.cpp)
#target_link_libraries(example ${PROJECT_NAME})

find_package(Eigen REQUIRED)
include_directories(${Eigen_INCLUDE_DIRS})
add_definitions(${EIGEN_DEFINITIONS})

include_directories(${catkin_INCLUDE_DIRS})

# CATKIN_MIGRATION: removed during catkin migration
# rosbuild_add_boost_directories()


add_library(${PROJECT_NAME}_tools src/tools/mechanism_interface.cpp
                                           src/tools/grasp_marker_publisher.cpp
                                           src/tools/vector_tools.cpp
                                           src/tools/convert_functions.cpp
                                           include/object_manipulator/tools/msg_helpers.h
                                           include/object_manipulator/tools/configuration_loader.h
                                           include/object_manipulator/tools/arm_configurations.h
                                           include/object_manipulator/tools/hand_description.h
                                           include/object_manipulator/tools/camera_configurations.h
                                           src/tools/shape_tools.cpp
					   src/tools/ik_tester_fast.cpp
                                           )
target_link_libraries(${PROJECT_NAME}_tools ${Boost_LIBRARIES})

add_dependencies(${PROJECT_NAME}_tools object_manipulation_msgs_generate_messages_cpp)
add_library(${PROJECT_NAME}_grasp_execution src/tools/mechanism_interface.cpp
                                                     src/grasp_execution/grasp_tester_fast.cpp
						     src/grasp_execution/approach_lift_grasp.cpp)
target_link_libraries(${PROJECT_NAME}_grasp_execution ${PROJECT_NAME}_tools)
						   
add_library(${PROJECT_NAME}_place_execution src/place_execution/descend_retreat_place.cpp
                                                     src/place_execution/place_tester_fast.cpp)
target_link_libraries(${PROJECT_NAME}_place_execution ${PROJECT_NAME}_tools)

add_library(${PROJECT_NAME} src/object_manipulator.cpp)
target_link_libraries(${PROJECT_NAME} ${PROJECT_NAME}_tools
                                      ${PROJECT_NAME}_grasp_execution
                                      ${PROJECT_NAME}_place_execution)

add_executable(object_manipulator_node nodes/object_manipulator_node.cpp)
target_link_libraries(object_manipulator_node ${PROJECT_NAME}_tools
                                              ${PROJECT_NAME}_grasp_execution
                                              ${PROJECT_NAME}_place_execution
                                              ${PROJECT_NAME})

## Generate added messages and services with any dependencies listed here

# catkin_package parameters: http://ros.org/doc/groovy/api/catkin/html/dev_guide/generated_cmake_api.html#catkin-package
# TODO: fill in what other packages will need to use this package
catkin_package(
    DEPENDS roscpp rospy tf pluginlib std_srvs actionlib object_manipulation_msgs sensor_msgs  pr2_controllers_msgs geometry_msgs visualization_msgs pr2_mechanism_msgs eigen_conversions common_rosdeps python python-scipy
    CATKIN_DEPENDS # TODO
    INCLUDE_DIRS include# TODO include
    LIBRARIES # TODO
)

install(DIRECTORY include/${PROJECT_NAME} DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

