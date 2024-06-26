cmake_minimum_required(VERSION 2.8.3)
project(icp_pose_corrector)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++14" COMPILER_SUPPORTS_CXX14)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX14)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")
elseif(COMPILER_SUPPORTS_CXX11)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
    	message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

set(CMAKE_BUILD_TYPE Debug)

find_package(catkin REQUIRED COMPONENTS
  dynamic_reconfigure
  laser_geometry
  pcl_conversions
  pcl_ros
  roscpp
  tf
  tf_conversions
)

generate_dynamic_reconfigure_options(
  cfg/IcpPoseCorrector.cfg
)

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES icp_pose_corrector
#  CATKIN_DEPENDS pcl_conversions pcl_ros roscpp tf
#  DEPENDS system_lib
)

include_directories(
 include
  ${catkin_INCLUDE_DIRS}
)

add_executable(icp_pose_corrector_node src/icp_pose_corrector_node.cpp src/icp_pose_corrector.cpp)

add_dependencies(icp_pose_corrector_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS} ${PROJECT_NAME}_gencfg)

target_link_libraries(icp_pose_corrector_node
  ${catkin_LIBRARIES}
)

install( DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  FILES_MATCHING PATTERN "*.launch"
)

install(DIRECTORY config/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/config
  FILES_MATCHING PATTERN "*.yaml"
)

install(TARGETS icp_pose_corrector_node RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

