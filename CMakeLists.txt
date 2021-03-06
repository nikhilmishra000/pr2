cmake_minimum_required(VERSION 2.8.3)
project(pr2_utils)

add_definitions("-Wno-sign-compare -std=c++11")

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS roscpp roslib actionlib sensor_msgs trajectory_msgs control_msgs)

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)
find_package(cmake_modules REQUIRED)
find_package(Eigen REQUIRED)
find_package(OpenRAVE REQUIRED)
find_package(Boost ${OpenRAVE_Boost_VERSION} EXACT COMPONENTS iostreams thread filesystem system program_options graph)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
# generate_messages(
#   DEPENDENCIES
#   std_msgs  # Or other packages containing msgs
# )

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES pr2_utils
#  CATKIN_DEPENDS other_catkin_pkg
#  DEPENDS OpenRAVE Boost
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include ${Eigen_INCLUDE_DIRS} ${OpenRAVE_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS})
# I believe the above ${Eigen_INCLUDE_DIRS} is now redundant

link_directories(${OpenRAVE_LIBRARY_DIRS} ${Boost_LIBRARY_DIRS})

## Declare a cpp library
# add_library(pr2_utils
#   src/${PROJECT_NAME}/pr2_utils.cpp
# )
add_library(pr2_utils
    src/${PROJECT_NAME}/pr2_sim/arm.cpp
    src/${PROJECT_NAME}/pr2_sim/simulator.cpp
    src/${PROJECT_NAME}/pr2_sim/camera.cpp
    src/${PROJECT_NAME}/pr2/arm.cpp
)

## Declare a cpp executable
add_executable(pr2_sim_arm         tests/pr2_sim_arm.cpp src/${PROJECT_NAME}/pr2_sim/arm.cpp
														 src/${PROJECT_NAME}/pr2_sim/simulator.cpp)
#add_executable(pr2_sim_simulator   tests/pr2_sim_simulator.cpp src/${PROJECT_NAME}/pr2_sim/arm.cpp
#                                                               src/${PROJECT_NAME}/pr2_sim/simulator.cpp)
#add_executable(pr2_sim_camera      tests/pr2_sim_camera.cpp src/${PROJECT_NAME}/pr2_sim/arm.cpp
#														    src/${PROJECT_NAME}/pr2_sim/simulator.cpp
#														    src/${PROJECT_NAME}/pr2_sim/camera.cpp)
add_executable(gradient_sd         tests/gradient_sd.cpp    src/${PROJECT_NAME}/pr2_sim/arm.cpp
														    src/${PROJECT_NAME}/pr2_sim/simulator.cpp
														    src/${PROJECT_NAME}/pr2_sim/camera.cpp)
#add_executable(pr2_arm			   tests/test_arm.cpp       src/${PROJECT_NAME}/pr2/arm.cpp
#                                                            src/${PROJECT_NAME}/pr2_sim/arm.cpp
#                                                            src/${PROJECT_NAME}/pr2_sim/simulator.cpp
#                                                            src/${PROJECT_NAME}/pr2_sim/camera.cpp)                                                     

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
# add_dependencies(pr2_utils_node pr2_utils_generate_messages_cpp)

set_target_properties(pr2_sim_arm PROPERTIES COMPILE_FLAGS "${OPENRAVE_CXXFLAGS}")
set_target_properties(pr2_sim_arm PROPERTIES LINK_FLAGS "${OPENRAVE_LINK_FLAGS}")

#set_target_properties(pr2_sim_simulator PROPERTIES COMPILE_FLAGS "${OPENRAVE_CXXFLAGS}")
#set_target_properties(pr2_sim_simulator PROPERTIES LINK_FLAGS "${OPENRAVE_LINK_FLAGS}")

#set_target_properties(pr2_sim_camera PROPERTIES COMPILE_FLAGS "${OPENRAVE_CXXFLAGS}")
#set_target_properties(pr2_sim_camera PROPERTIES LINK_FLAGS "${OPENRAVE_LINK_FLAGS}")

set_target_properties(gradient_sd PROPERTIES COMPILE_FLAGS "${OPENRAVE_CXXFLAGS}")
set_target_properties(gradient_sd PROPERTIES LINK_FLAGS "${OPENRAVE_LINK_FLAGS}")

#set_target_properties(pr2_arm PROPERTIES COMPILE_FLAGS "${OPENRAVE_CXXFLAGS}")
#set_target_properties(pr2_arm PROPERTIES LINK_FLAGS "${OPENRAVE_LINK_FLAGS}")

set(TIMER_LIBRARIES "dl;rt")

## Specify libraries to link a library or executable target against
target_link_libraries(pr2_sim_arm
   ${catkin_LIBRARIES}
   ${OpenRAVE_CORE_LIBRARIES}
   ${Boost_LIBRARIES}
)
#target_link_libraries(pr2_sim_simulator
#   ${catkin_LIBRARIES}
#   ${OpenRAVE_CORE_LIBRARIES}
#   ${Boost_LIBRARIES}
#)
#target_link_libraries(pr2_sim_camera
#   ${catkin_LIBRARIES}
#   ${OpenRAVE_CORE_LIBRARIES}
#   ${Boost_LIBRARIES}
#   ${TIMER_LIBRARIES}
#)
target_link_libraries(gradient_sd
   ${catkin_LIBRARIES}
   ${OpenRAVE_CORE_LIBRARIES}
   ${Boost_LIBRARIES}
   ${TIMER_LIBRARIES}
)
#target_link_libraries(pr2_arm
#   ${catkin_LIBRARIES}
#   ${OpenRAVE_CORE_LIBRARIES}
#   ${Boost_LIBRARIES}
#   ${TIMER_LIBRARIES}
#)

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS pr2_utils pr2_utils_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_pr2_utils.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
