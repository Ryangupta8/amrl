PROJECT(amrl)
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

SET(CMAKE_CXX_FLAGS "-march=native -Wall -Werror -g")

IF(${CMAKE_BUILD_TYPE} MATCHES "Release")
  MESSAGE(STATUS "Additional Flags for Release mode")
  SET(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fopenmp -fno-builtin-malloc \
      -fno-builtin-calloc -fno-builtin-realloc -fno-builtin-free -O3 -DNDEBUG")
  SET(BUILD_SPECIFIC_LIBRARIES "tcmalloc")
ELSEIF(${CMAKE_BUILD_TYPE} MATCHES "Debug")
  MESSAGE(STATUS "Additional Flags for Debug mode")
  SET(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -g")
  SET(BUILD_SPECIFIC_LIBRARIES "")
ENDIF()

MESSAGE(STATUS "SOURCE DIR: ${PROJECT_SOURCE_DIR}")


FIND_PACKAGE(Lua51 REQUIRED)
FIND_PACKAGE(Eigen3 REQUIRED)
FIND_PACKAGE(Ceres REQUIRED)

INCLUDE($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
ROSBUILD_INIT()
SET(ROS_BUILD_STATIC_LIBS true)
SET(ROS_BUILD_SHARED_LIBS false)

SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

INCLUDE_DIRECTORIES(${PROJECT_SOURCE_DIR})

ADD_SUBDIRECTORY(enml/src/shared)

ROSBUILD_ADD_EXECUTABLE(enml
    enml/src/non_markov_localization_main.cpp
    enml/src/non_markov_localization.cpp
    enml/src/kdtree.cpp
    enml/src/perception_2d.cpp
    enml/src/vector_map/vector_map.cc
    enml/src/visualization/visualization.cc
    enml/src/visualization/gui_helpers.cc)
TARGET_INCLUDE_DIRECTORIES(enml PRIVATE
    enml/src
    enml/src/shared
    enml/src/config-reader/include
    ${LUA_INCLUDE_DIR}
    ${CERES_INCLUDE_DIRS})
ADD_DEPENDENCIES(enml rosbuild_precompile)
TARGET_LINK_LIBRARIES(enml ceres roslib roscpp ncurses pthread boost_system
    ${LUA_LIBRARIES} rt popt glog rosbag amrl_shared_lib
    ${BUILD_SPECIFIC_LIBRARIES})

ROSBUILD_ADD_EXECUTABLE(navigation
    graph_navigation/src/navigation/navigation_main.cc
    graph_navigation/src/navigation/navigation.cc
    graph_navigation/src/visualization/visualization.cc
    graph_navigation/src/vector_map/vector_map.cc)
TARGET_INCLUDE_DIRECTORIES(navigation PRIVATE
    graph_navigation/src
    enml/src/shared
    deps/config_reader/include
    ${LUA_INCLUDE_DIR})
TARGET_LINK_LIBRARIES(navigation gflags roslib roscpp pthread boost_system
    rt glog rosbag amrl_shared_lib ${LUA_LIBRARIES}
    ${BUILD_SPECIFIC_LIBRARIES})

