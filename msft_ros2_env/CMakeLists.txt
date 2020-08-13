cmake_minimum_required(VERSION 3.5)
project(msft_ros2_env NONE)

# This is inspired by ros_workspace project.
# https://github.com/ros2/ros_workspace

# If you edit this file for the first time, it's recommended to read this.
# https://colcon.readthedocs.io/en/released/developer/environment.html

find_package(ament_cmake_core REQUIRED)

get_filename_component(PYTHONHOME "${PYTHON_EXECUTABLE}" DIRECTORY)
file(TO_NATIVE_PATH "${PYTHONHOME}" PYTHONHOME)

# Bootstrap Python Runtime Location
set(EXTERNAL_PREFIX_CONTENT "")
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${PYTHONHOME}")
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${PYTHONHOME}\\Scripts")
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nset;PYTHONHOME;${PYTHONHOME}")

set(EXTERNAL_PREFIX_BATCH_CONTENT "")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=%PATH:${PYTHONHOME};=%\"")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=${PYTHONHOME};%PATH%\"")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=%PATH:${PYTHONHOME}\\Scripts;=%\"")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=${PYTHONHOME}\\Scripts;%PATH%\"")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PYTHONHOME=${PYTHONHOME}\"")

# Bootstrap CMAKE_PREFIX_PATH
foreach(_PREFIX $ENV{CMAKE_PREFIX_PATH})
    file(TO_NATIVE_PATH "${_PREFIX}" NATIVE_PREFIX)
    set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${NATIVE_PREFIX}\\bin")
    set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;CMAKE_PREFIX_PATH;${NATIVE_PREFIX}")
    set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;AMENT_PREFIX_PATH;${NATIVE_PREFIX}")

    
    set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=%PATH:${NATIVE_PREFIX}\\bin;=%\"")
    set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=${NATIVE_PREFIX}\\bin;%PATH%\"")
    
    set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"CMAKE_PREFIX_PATH=%CMAKE_PREFIX_PATH:${NATIVE_PREFIX};=%\"")
    set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"CMAKE_PREFIX_PATH=${NATIVE_PREFIX};%CMAKE_PREFIX_PATH%\"")
    
    set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"AMENT_PREFIX_PATH=%AMENT_PREFIX_PATH:${NATIVE_PREFIX};=%\"")
    set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"AMENT_PREFIX_PATH=${NATIVE_PREFIX};%AMENT_PREFIX_PATH%\"")
endforeach()

# Bootstrap Python DLLs
# Since Python3.7, the Python started to vendor openssl DLLs.
# It is important to make sure we keep that set to be loaded first at runtime.
# We add it to the front of the search path to ensure the order.
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${PYTHONHOME}\\DLLs")

set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=%PATH:${PYTHONHOME}\\DLLs;=%\"")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"PATH=${PYTHONHOME}\\DLLs;%PATH%\"")

# Bootstrap QT platform plugin DLLs
# TODO: To find what package vendor the platform DLLs and move the logic over.
file(TO_NATIVE_PATH "${CMAKE_INSTALL_PREFIX}" _NATIVE_INSTALL_PREFIX)
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;QT_QPA_PLATFORM_PLUGIN_PATH;${_NATIVE_INSTALL_PREFIX}\\bin\\platforms")

set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"QT_QPA_PLATFORM_PLUGIN_PATH=%QT_QPA_PLATFORM_PLUGIN_PATH:${_NATIVE_INSTALL_PREFIX}\\bin\\platforms;=%\"")
set(EXTERNAL_PREFIX_BATCH_CONTENT "${EXTERNAL_PREFIX_BATCH_CONTENT}\nset \"QT_QPA_PLATFORM_PLUGIN_PATH=${_NATIVE_INSTALL_PREFIX}\\bin\\platforms;%QT_QPA_PLATFORM_PLUGIN_PATH%\"")

# batch script hook is generated for Dashing
ament_environment_hooks("env-hooks/external_prefix.dsv.in" "env-hooks/external_prefix.bat.in")

ament_package()
ament_generate_environment()
