# Motoman ROS2 port project

https://github.com/users/ppbrown/projects/1

Currently, motoman_msgs and motoman_resources build

in theory motoman_gp7_support would compile.. 
but we havent ported its required dependancies yet

motoman_driver should have correct CMakeLists.txt and package,
but it definitely is missing dependancies at present, plus will
most likely need code changes for ROS2

## Building

A reminder to those new to ROS2, for steps to theoretically compile.

0. (install ROS2 packages, etc)
1. mkdir -p ~/ros/src
2. extract this repo under that src directory
3. extract repos for all the dependancies there too
    (maybe someday we'll support rosdep)
4. cd ~/ros
5. colcon build


