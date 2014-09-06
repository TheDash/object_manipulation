cmake_minimum_required(VERSION 2.8.3)
project(point_cloud_server)
find_package(catkin REQUIRED COMPONENTS roscpp interactive_markers sensor_msgs visualization_msgs geometry_msgs tf actionlib object_manipulator pcl_ros)

find_package(PCL REQUIRED)
include_directories(include ${PCL_INCLUDE_DIRS})

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

add_action_files(
  FILES
  StoreCloud.action
)
add_executable(point_cloud_server
src/cloud_server.cpp
src/cloud_handler.cpp
)

include_directories(${Boost_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${pcl_ros_tf_LIBRARIES})


generate_messages(
    DEPENDENCIES geometry_msgs std_msgs sensor_msgs actionlib_msgs
)

catkin_package(
    DEPENDS roscpp interactive_markers sensor_msgs visualization_msgs geometry_msgs tf actionlib object_manipulator pcl_ros
    CATKIN_DEPENDS # TODO
    INCLUDE_DIRS # TODO include
    LIBRARIES # TODO
)

install(DIRECTORY launch/ DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})


