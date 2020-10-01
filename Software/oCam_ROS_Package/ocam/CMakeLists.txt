cmake_minimum_required(VERSION 3.0.2)
project(ocam)

add_compile_options(-Wall -Wextra -Wno-psabi)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# add_definitions(-std=c++11)
#set(CMAKE_CXX_FLAGS "-std=c++11 -O2 ${CMAKE_CXX_FLAGS}")

find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  cv_bridge
  image_transport
  camera_info_manager
  sensor_msgs
  dynamic_reconfigure
)

find_package(Boost REQUIRED COMPONENTS system)
find_package(OpenCV REQUIRED)

generate_dynamic_reconfigure_options(
  cfg/cam.cfg
)

catkin_package(
  INCLUDE_DIRS include
	LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS
    roscpp
    std_msgs
    cv_bridge
    image_transport
    camera_info_manager
    sensor_msgs
    dynamic_reconfigure
)

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
	include/${PROJECT_NAME}
  ${catkin_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME}
  src/${PROJECT_NAME}/withrobot_camera.cpp
	src/${PROJECT_NAME}/withrobot_utility.cpp
)

add_dependencies(${PROJECT_NAME}
 								 ${PROJECT_NAME}_gencfg
								 ${${PROJECT_NAME}_EXPORTED_TARGETS}
								 ${catkin_EXPORTED_TARGETS})

# add_executable(ocam src/oCam.cpp src/withrobot_camera.cpp src/withrobot_utility.cpp)
add_executable(${PROJECT_NAME}_ocam src/oCam.cpp)

set_target_properties(${PROJECT_NAME}_ocam PROPERTIES OUTPUT_NAME ocam PREFIX "")

target_link_libraries(${PROJECT_NAME}_ocam
											${PROJECT_NAME}
											${catkin_LIBRARIES}
											${Boost_LIBRARIES}
											${OpenCV_LIBS}
											 -lv4l2 -ludev)

install(TARGETS
	${PROJECT_NAME}_ocam
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)
