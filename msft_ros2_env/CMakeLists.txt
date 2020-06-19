cmake_minimum_required(VERSION 3.5)
project(msft_ros2_env NONE)

# This is inspired by ros_workspace project.
# https://github.com/ros2/ros_workspace

find_package(ament_cmake_core REQUIRED)

get_filename_component(PYTHONHOME "${PYTHON_EXECUTABLE}" DIRECTORY)
file(TO_NATIVE_PATH "${PYTHONHOME}" PYTHONHOME)

# Bootstrap Python Runtime Location
set(EXTERNAL_PREFIX_CONTENT "")
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${PYTHONHOME}")
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${PYTHONHOME}\\Scripts")
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nset;PYTHONHOME;${PYTHONHOME}")

# Bootstrap CMAKE_PREFIX_PATH
foreach(_PREFIX $ENV{CMAKE_PREFIX_PATH})
    file(TO_NATIVE_PATH "${_PREFIX}" NATIVE_PREFIX)
    set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${NATIVE_PREFIX}\\bin")
    set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;CMAKE_PREFIX_PATH;${NATIVE_PREFIX}")
    set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;AMENT_PREFIX_PATH;${NATIVE_PREFIX}")
endforeach()

# Bootstrap Python DLLs
# Since Python3.7, the Python started to vendor openssl DLLs.
# It is important to make sure we keep that set to be loaded first at runtime.
# We add it to the front of the search path to ensure the order.
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;PATH;${PYTHONHOME}\\DLLs")

# Bootstrap QT platform plugin DLLs
# TODO: To find what package vendor the platform DLLs and move the logic over.
file(TO_NATIVE_PATH "${CMAKE_INSTALL_PREFIX}" _NATIVE_INSTALL_PREFIX)
set(EXTERNAL_PREFIX_CONTENT "${EXTERNAL_PREFIX_CONTENT}\nprepend-non-duplicate;QT_QPA_PLATFORM_PLUGIN_PATH;${_NATIVE_INSTALL_PREFIX}\\bin\\platforms")

ament_environment_hooks("env-hooks/external_prefix.dsv.in")

ament_package()
ament_generate_environment()
