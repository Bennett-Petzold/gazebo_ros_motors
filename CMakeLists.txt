cmake_minimum_required(VERSION 3.8)
project(gazebo_ros_motors)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

include (FindPkgConfig)
if (PKG_CONFIG_FOUND)
  pkg_check_modules(XML libxml-2.0)
  pkg_check_modules(OGRE OGRE)
  pkg_check_modules(OGRE-Terrain OGRE-Terrain)
  pkg_check_modules(OGRE-Paging OGRE-Paging)
else()
  message(FATAL_ERROR "pkg-config is required; please install it")
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)
find_package(gazebo REQUIRED)
find_package(gazebo_ros REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

execute_process(COMMAND
  pkg-config --variable=plugindir OGRE
  OUTPUT_VARIABLE OGRE_PLUGIN_PATH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

include_directories(include
  ${Boost_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS}
  ${OGRE_INCLUDE_DIRS}
  ${OGRE-Terrain_INCLUDE_DIRS}
  ${OGRE-Paging_INCLUDE_DIRS}
)

link_directories(
  ${catkin_LIBRARY_DIRS}
  ${OGRE_LIBRARY_DIRS}
  ${OGRE-Terrain_LIBRARY_DIRS}
  ${OGRE-Paging_LIBRARY_DIRS}
)

#generate_dynamic_reconfigure_options(
# cfg/motorModel.cfg
#)

###########
## Build ##
###########

ament_export_dependencies(rosidl_default_runtime)

add_library(gazebo_ros_dc_motor src/gazebo_ros_dc_motor.cpp)
target_link_libraries(gazebo_ros_dc_motor gazebo_ros_utils ${catkin_LIBRARIES} ${Boost_LIBRARIES})
#add_dependencies(gazebo_ros_dc_motor ${PROJECT_NAME}_gencfg)
ament_target_dependencies(gazebo_ros_dc_motor rclcpp sensor_msgs std_msgs gazebo_ros nav_msgs)

add_library(gazebo_ros_joint_motor src/gazebo_ros_joint_motor.cpp)
target_link_libraries(gazebo_ros_joint_motor gazebo_ros_utils ${catkin_LIBRARIES} ${Boost_LIBRARIES})
ament_target_dependencies(gazebo_ros_joint_motor rclcpp sensor_msgs std_msgs gazebo_ros)

##########
## Test ##
##########

# if(CATKIN_ENABLE_TESTING)
# find_package(rostest REQUIRED)
#
# add_rostest_gtest(test_motor_model test/test_motor_model.test src/test/test_motor_model.cpp)
# target_link_libraries(test_motor_model ${catkin_LIBRARIES})
#
# endif()

#############
## Install ##
#############

#rosidl_target_interfaces(gazebo_ros_dc_motor ${PROJECT_NAME} "rosidl_typesupport_cpp")
#rosidl_target_interfaces(gazebo_ros_joint_motor ${PROJECT_NAME} "rosidl_typesupport_cpp")

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

install(TARGETS
  gazebo_ros_dc_motor
  DESTINATION lib/${PROJECT_NAME}
  )

install(TARGETS
  gazebo_ros_joint_motor
  DESTINATION lib/${PROJECT_NAME}
  )

#install(DIRECTORY include/
	#DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
   #FILES_MATCHING PATTERN "*.h"
   #)

ament_package()
