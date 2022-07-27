cmake_minimum_required(VERSION 2.8.3)
project(db_alpha_controllers)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++11)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
   roscpp
   dynamixel_workbench_msgs
   dynamixel_workbench_toolbox
   cmake_modules
   sensor_msgs
   geometry_msgs
   trajectory_msgs
)

# Resolve system dependency on yaml-cpp, which apparently does not
# provide a CMake find_package() module.
find_package(Eigen3 REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(YAML_CPP REQUIRED yaml-cpp)
find_path(YAML_CPP_INCLUDE_DIR
  NAMES yaml_cpp.h
  PATHS ${YAML_CPP_INCLUDE_DIRS}
)
find_library(YAML_CPP_LIBRARY
  NAMES YAML_CPP
  PATHS ${YAML_CPP_LIBRARY_DIRS}
)
link_directories(${YAML_CPP_LIBRARY_DIRS})

if(NOT ${YAML_CPP_VERSION} VERSION_LESS "0.5")
add_definitions(-DHAVE_NEW_YAMLCPP)
endif(NOT ${YAML_CPP_VERSION} VERSION_LESS "0.5")

################################################
## Declare ROS messages, services and actions ##
################################################


################################################
## Declare ROS dynamic reconfigure parameters ##
################################################



###################################
## catkin specific configuration ##
###################################

catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS roscpp sensor_msgs geometry_msgs dynamixel_workbench_msgs trajectory_msgs dynamixel_workbench_toolbox cmake_modules
  DEPENDS EIGEN3
#  LIBRARIES open_manipulator_controllers
#  CATKIN_DEPENDS other_catkin_pkg
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
#  ${EIGEN3_INCLUDE_DIRS}
  ${YAML_CPP_INCLUDE_DIRS}
)

add_executable(velocity_controller src/velocity_controller.cpp)
add_dependencies(velocity_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(velocity_controller ${catkin_LIBRARIES} ${Eigen3_LIBRARIES} ${YAML_CPP_LIBRARIES})

add_executable(torque_controller src/torque_controller.cpp)
add_dependencies(torque_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(torque_controller ${catkin_LIBRARIES} ${Eigen3_LIBRARIES} ${YAML_CPP_LIBRARIES})

add_executable(position_controller src/position_controller.cpp)
add_dependencies(position_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(position_controller ${catkin_LIBRARIES} ${Eigen3_LIBRARIES} ${YAML_CPP_LIBRARIES})

add_executable(hexapod_controller src/hexapod_controller.cpp)
add_dependencies(hexapod_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(hexapod_controller ${catkin_LIBRARIES} ${Eigen3_LIBRARIES} ${YAML_CPP_LIBRARIES})

add_executable(one_leg_controller src/one_leg_controller.cpp)
add_dependencies(one_leg_controller ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(one_leg_controller ${catkin_LIBRARIES} ${Eigen3_LIBRARIES} ${YAML_CPP_LIBRARIES})

add_executable(reboot src/reboot.cpp)
add_dependencies(reboot ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(reboot ${catkin_LIBRARIES} ${Eigen3_LIBRARIES} ${YAML_CPP_LIBRARIES})

#############
## Install ##
#############
install(TARGETS torque_controller position_controller hexapod_controller
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(DIRECTORY launch config
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

catkin_install_python(PROGRAMS scripts/cpg.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

#############
## Testing ##
#############

