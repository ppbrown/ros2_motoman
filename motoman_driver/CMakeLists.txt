cmake_minimum_required(VERSION 3.5)
project(motoman_driver)

# require C++11
#set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Load catkin and all dependencies required for this package
find_package( ament_cmake REQUIRED)
find_package( actionlib_msgs REQUIRED)
#find_package( control_msgs REQUIRED)
find_package( industrial_msgs REQUIRED)
find_package( industrial_robot_client REQUIRED)
find_package( industrial_utils REQUIRED)
find_package( motoman_msgs REQUIRED)
find_package( rclcpp REQUIRED)
find_package( sensor_msgs REQUIRED)
find_package( simple_message REQUIRED)
find_package( std_msgs REQUIRED)
find_package( std_srvs REQUIRED)
find_package( trajectory_msgs REQUIRED)
find_package( urdf REQUIRED)

find_package(Boost REQUIRED COMPONENTS system thread)

#######################################
## Adding directories and definitions #
#######################################
include_directories(
  include
  ${Boost_INCLUDE_DIRS}
  )

add_definitions(-DLINUXSOCKETS=1)  #use linux sockets for communication
add_definitions(-DROS=1)


set(MSG_SRC_FILES
  src/simple_message/messages/motoman_motion_ctrl_message.cpp
  src/simple_message/messages/motoman_motion_reply_message.cpp
  src/simple_message/messages/motoman_read_single_io_message.cpp
  src/simple_message/messages/motoman_read_single_io_reply_message.cpp
  src/simple_message/messages/motoman_write_single_io_message.cpp
  src/simple_message/messages/motoman_write_single_io_reply_message.cpp
  src/simple_message/motoman_motion_ctrl.cpp
  src/simple_message/motoman_motion_reply.cpp
  src/simple_message/motoman_read_single_io.cpp
  src/simple_message/motoman_read_single_io_reply.cpp
  src/simple_message/motoman_write_single_io.cpp
  src/simple_message/motoman_write_single_io_reply.cpp
)

set(CLIENT_SRC_FILES
  src/industrial_robot_client/joint_feedback_ex_relay_handler.cpp
  src/industrial_robot_client/joint_feedback_relay_handler.cpp
  src/industrial_robot_client/joint_relay_handler.cpp
  src/industrial_robot_client/joint_trajectory_interface.cpp
  src/industrial_robot_client/joint_trajectory_streamer.cpp
  src/industrial_robot_client/motoman_utils.cpp
  src/industrial_robot_client/robot_state_interface.cpp
  src/simple_message/joint_feedback_ex.cpp
  src/simple_message/joint_traj_pt_full_ex.cpp
  src/simple_message/messages/joint_feedback_ex_message.cpp
  src/simple_message/messages/joint_traj_pt_full_ex_message.cpp
)


###########
## Build ##
###########

#-------------------------------------------------------------
# dx100 uses same byte-ordering as most i386-based PCs

# Simple message library
add_library(motoman_simple_message ${MSG_SRC_FILES})
target_link_libraries(motoman_simple_message
  simple_message)

# Industrial client library
add_library(motoman_industrial_robot_client ${CLIENT_SRC_FILES})
target_link_libraries(motoman_industrial_robot_client
  industrial_robot_client
  industrial_utils)
target_compile_definitions(motoman_industrial_robot_client PUBLIC ${industrial_robot_client_DEFINITIONS})

# Robot state node
add_executable(motoman_robot_state
  src/robot_state_node.cpp)
target_link_libraries(motoman_robot_state
  motoman_simple_message
  motoman_industrial_robot_client
  )
set_target_properties(motoman_robot_state
  PROPERTIES OUTPUT_NAME robot_state
  PREFIX "")

# Motion streaming node
add_executable(motoman_motion_streaming_interface
  src/joint_streaming_node.cpp
  src/joint_trajectory_streamer.cpp
  src/motion_ctrl.cpp
  src/io_ctrl.cpp)
target_link_libraries(motoman_motion_streaming_interface
  motoman_simple_message
  motoman_industrial_robot_client
  })
set_target_properties(motoman_motion_streaming_interface
  PROPERTIES OUTPUT_NAME motion_streaming_interface
  PREFIX "")

# I/O relay node
add_executable(motoman_io_relay
  src/io_relay_node.cpp
  src/io_relay.cpp
  src/io_ctrl.cpp)
target_link_libraries(motoman_io_relay
  motoman_simple_message
  motoman_industrial_robot_client
  )
set_target_properties(motoman_io_relay
  PROPERTIES OUTPUT_NAME io_relay
  PREFIX "")


#----------------------------------------------------------------
# FS100 uses opposite byte-ordering from most i386-based PCs
# Simple message library
add_library(motoman_simple_message_bswap ${MSG_SRC_FILES})
set_target_properties(motoman_simple_message_bswap PROPERTIES COMPILE_DEFINITIONS "BYTE_SWAPPING")
target_link_libraries(motoman_simple_message_bswap
  simple_message_bswap)

# Industrial client library
add_library(motoman_industrial_robot_client_bswap ${CLIENT_SRC_FILES})
set_target_properties(motoman_industrial_robot_client_bswap PROPERTIES COMPILE_DEFINITIONS "BYTE_SWAPPING")
target_link_libraries(motoman_industrial_robot_client_bswap
  industrial_robot_client_bswap
  industrial_utils)
target_compile_definitions(motoman_industrial_robot_client_bswap PUBLIC ${industrial_robot_client_bswap_DEFINITIONS})

# Robot state node
add_executable(motoman_robot_state_bswap
  src/robot_state_node.cpp)
target_link_libraries(motoman_robot_state_bswap
  motoman_simple_message_bswap
  motoman_industrial_robot_client_bswap
  )
set_target_properties(motoman_robot_state_bswap
  PROPERTIES OUTPUT_NAME robot_state_bswap
  PREFIX "")

# Motion streaming node
add_executable(motoman_motion_streaming_interface_bswap
  src/joint_streaming_node.cpp
  src/joint_trajectory_streamer.cpp
  src/motion_ctrl.cpp
  src/io_ctrl.cpp)
target_link_libraries(motoman_motion_streaming_interface_bswap
  motoman_simple_message_bswap
  motoman_industrial_robot_client_bswap
  )
set_target_properties(motoman_motion_streaming_interface_bswap
  PROPERTIES OUTPUT_NAME motion_streaming_interface_bswap
  PREFIX "")

# I/O relay node
add_executable(motoman_io_relay_bswap
  src/io_relay_node.cpp
  src/io_relay.cpp
  src/io_ctrl.cpp)
target_link_libraries(motoman_io_relay_bswap
  motoman_simple_message_bswap
  motoman_industrial_robot_client_bswap
  )
set_target_properties(motoman_io_relay_bswap
  PROPERTIES OUTPUT_NAME io_relay_bswap
  PREFIX "")


add_executable(${PROJECT_NAME}_joint_trajectory_action
  src/industrial_robot_client/joint_trajectory_action.cpp
  src/joint_trajectory_action_node.cpp)
target_link_libraries(${PROJECT_NAME}_joint_trajectory_action
  industrial_robot_client
  motoman_industrial_robot_client
  )


#############
## Install ##
#############

# binaries
install(TARGETS
  ${PROJECT_NAME}_joint_trajectory_action
  motoman_io_relay
  motoman_io_relay_bswap
  motoman_motion_streaming_interface
  motoman_motion_streaming_interface_bswap
  motoman_robot_state
  motoman_robot_state_bswap

  DESTINATION lib/${PROJECT_NAME}
)

# libraries
install(TARGETS
  motoman_industrial_robot_client
  motoman_industrial_robot_client_bswap
  motoman_simple_message
  motoman_simple_message_bswap

  DESTINATION lib
)

# headers
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
)

# other files
install(DIRECTORY
    Inform
    launch
    MotoPlus
  DESTINATION share/${PROJECT_NAME}
)

#############
## Testing ##
#############

## if(CATKIN_ENABLE_TESTING)
##   find_package(roslint REQUIRED)
  # gather files, ignoring MotoPlus (we can't use '--exclude' (not supported
  # by roslint's version ofcpplint) and we don't want to add a CPPLINT.cfg
  # file to MotoPlus)
  ##   file(GLOB_RECURSE files_to_cpplint RELATIVE ${CMAKE_CURRENT_LIST_DIR} "*.cpp" "*.h")
  ##   string(REGEX REPLACE "MotoPlus/[^;]+;?" "" files_to_cpplint "${files_to_cpplint}")
  ##   roslint_cpp(${files_to_cpplint})
  ##   roslint_add_test()

  ##   find_package(roslaunch REQUIRED)
  ##   roslaunch_add_file_check(tests/roslaunch_test_io_relay.xml)
  ## endif()

ament_package()
