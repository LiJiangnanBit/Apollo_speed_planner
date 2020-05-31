cmake_minimum_required(VERSION 2.8.3)
project(apollo_speed)

## Compile as C++11, supported in ROS Kinetic and newer
add_definitions(-std=c++11 -w)
set(CMAKE_CXX_FLAGS " -Wall -Wextra ${CMAKE_CXX_FLAGS}")
set(CMAKE_BUILD_TYPE "Release")

set(catkin_deps
        roscpp
        )
find_package(catkin REQUIRED COMPONENTS ${catkin_deps})

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
find_package(IPOPT REQUIRED)
find_package(Eigen3 REQUIRED)
#find_package(osqp REQUIRED)
#find_package(OsqpEigen REQUIRED)
#include(FindProtobuf)
find_package(Protobuf REQUIRED)

catkin_package(
        INCLUDE_DIRS include
        LIBRARIES ${PROJECT_NAME}
        CATKIN_DEPENDS ${catkin_deps}
        DEPENDS
)

include_directories(
        include
        osqp/include
        ${Protobuf_INCLUDE_DIRS}
        ${catkin_INCLUDE_DIRS}
        ${EIGEN3_INCLUDE_DIR}
)

link_directories(osqp/lib)
add_library(${PROJECT_NAME} src/PiecewiseJerkSpeedOptimizer.cpp include/PiecewiseJerkSpeedOptimizer.hpp
        src/IpoptInterface.cpp include/IpoptInterface.hpp src/PiecewiseJerkProblem.cpp include/PiecewiseJerkProblem.hpp src/PiecewiseJerkSpeedProblem.cpp include/PiecewiseJerkSpeedProblem.hpp src/SpeedLimit.cpp include/SpeedLimit.hpp
        src/SpeedPoint.cpp include/SpeedPoint.hpp src/SpeedData.cpp include/SpeedData.hpp include/math.hpp src/PathPoint.cpp include/PathPoint.hpp src/DiscretizedPath.cpp include/DiscretizedPath.hpp src/PiecewiseJerkPathProblem.cpp include/PiecewiseJerkPathProblem.hpp src/PiecewiseJerkTrajectory1d.cpp include/PiecewiseJerkTrajectory1d.hpp include/Curve1d.hpp src/ConstantJerkTrajectory1d.cpp include/ConstantJerkTrajectory1d.hpp src/math.cpp src/PiecewiseJerkSpeedNonlinearIpoptInterface.cpp include/PiecewiseJerkSpeedNonlinearIpoptInterface.hpp src/SpeedProfileGenerator.cpp include/SpeedProfileGenerator.hpp)
target_link_libraries(${PROJECT_NAME} glog ${IPOPT_LIBRARIES} ${catkin_LIBRARIES} osqp
        )

add_executable(demo src/demo.cpp)
target_link_libraries(demo ${PROJECT_NAME})