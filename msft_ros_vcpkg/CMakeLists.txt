cmake_minimum_required(VERSION 2.8.3)
project(msft_ros_vcpkg)
find_package(catkin REQUIRED)

catkin_package()

set(ROS_VERSION "1")

# allow overriding the Python version
if(DEFINED ENV{ROS_PYTHON_VERSION})
  set(ROS_PYTHON_VERSION "$ENV{ROS_PYTHON_VERSION}")
else()
  set(ROS_PYTHON_VERSION "2")
endif()

# allow overriding the distro name
if(DEFINED ENV{ROS_DISTRO_OVERRIDE})
  set(ROS_DISTRO $ENV{ROS_DISTRO_OVERRIDE})
else()
  set(ROS_DISTRO "melodic")
endif()

set(
  hooks
  "msft_ros_vcpkg"
)
if(CMAKE_HOST_UNIX)
  set(shell "sh")
else()
  set(shell "bat")
endif()
foreach(hook ${hooks})
  catkin_add_env_hooks("${hook}" SHELLS ${shell} DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/env-hooks)
endforeach()
