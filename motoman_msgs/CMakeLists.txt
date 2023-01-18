cmake_minimum_required(VERSION 3.5)
project(motoman_msgs)

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)

find_package(rosidl_default_generators REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(std_msgs REQUIRED)
find_package(industrial_msgs REQUIRED)
find_package(trajectory_msgs REQUIRED)

set(msg_files
    "msg/DynamicJointPoint.msg"
    "msg/DynamicJointsGroup.msg"
    "msg/DynamicJointState.msg"
    "msg/DynamicJointTrajectory.msg"
    "msg/DynamicJointTrajectoryFeedback.msg"
    #    "msg/MotorosError.msg"
    #    "msg/PointStreamFailure.msg"
)

set(srv_files
    "srv/CmdJointTrajectoryEx.srv"
    "srv/ReadSingleIO.srv"
    "srv/WriteSingleIO.srv"
    "srv/ReadMRegister.srv"
    "srv/ReadGroupIO.srv"
    "srv/SelectTool.srv"
    "srv/WriteMRegister.srv"
    "srv/WriteGroupIO.srv"
)

rosidl_generate_interfaces(${PROJECT_NAME}
	${msg_files}
	${srv_files}
	DEPENDENCIES builtin_interfaces std_msgs industrial_msgs trajectory_msgs
	)

ament_export_dependencies(rosidl_default_runtime)

ament_package()
