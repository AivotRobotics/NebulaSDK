cmake_minimum_required(VERSION 2.8.3)
project(VzenseROS_MultiCameras)

## Add support for C++11, supported in ROS melodic and newer
add_definitions(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  sensor_msgs
  message_generation
  dynamic_reconfigure
  camera_info_manager
  image_transport
  tf2
  tf2_ros
  tf2_geometry_msgs
  cv_bridge
)

# ==============================================
# Python scripts setup
# ==============================================
catkin_python_setup()

generate_dynamic_reconfigure_options(
    cfg/Vzensetof_roscpp.cfg
)

# ==============================================
# Setup
# ==============================================
catkin_package(CATKIN_DEPENDS camera_info_manager image_transport tf2 tf2_ros geometry_msgs message_runtime roscpp rospy cv_bridge)

# ==============================================
# Vzense setup
# ==============================================
find_package(OpenCV REQUIRED)
find_library(libNebula_api NAMES libNebula_api.so PATHS dependencies/Lib/)
include_directories(include include dependencies/Include ${catkin_INCLUDE_DIRS} ${OpenCV_INCLUDE_DIRS})

add_executable(vzense_multicameras src/vzense_driver.cpp
                                 src/vzense_manager.cpp
                                 include/vzense_manager.hpp)

add_dependencies(vzense_multicameras ${PROJECT_NAME}_gencfg)

target_link_libraries(vzense_multicameras ${catkin_LIBRARIES} ${libNebula_api} ${OpenCV_LIBRARIES})
add_dependencies(vzense_multicameras ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


# ==============================================
# Install
# ==============================================
install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

file(GLOB python_scripts_DIR "scripts/*.py")
catkin_install_python(PROGRAMS ${python_scripts_DIR} DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})
