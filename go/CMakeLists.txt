cmake_minimum_required(VERSION 2.8.3)
project(go)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  geometry_msgs
  message_generation
  message_runtime
)

# add_message_files(
#   FILES
#   action.msg
# )

 add_service_files(
   FILES
   setpoint.srv
   check.srv
   check_saved.srv
   new.srv
   save.srv
   picker_with_y.srv
 )

 generate_messages(
   DEPENDENCIES
   std_msgs
 )

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES go
  CATKIN_DEPENDS roscpp rospy std_msgs message_runtime
#  DEPENDS system_lib
)


include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)

