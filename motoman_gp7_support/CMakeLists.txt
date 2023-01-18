cmake_minimum_required(VERSION 3.5)

project(motoman_gp7_support)

find_package(ament_cmake REQUIRED)


#if (CATKIN_ENABLE_TESTING)
#  find_package(roslaunch REQUIRED)
#  roslaunch_add_file_check(test/launch_test.xml)
#endif()

install(DIRECTORY config/ 
  DESTINATION share/${PROJECT_NAME})
install(DIRECTORY launch/ 
  DESTINATION share/${PROJECT_NAME})
install(DIRECTORY meshes/
  DESTINATION share/${PROJECT_NAME})
install(DIRECTORY urdf/
  DESTINATION share/${PROJECT_NAME})

ament_package()
