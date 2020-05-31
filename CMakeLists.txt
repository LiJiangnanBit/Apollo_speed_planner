cmake_minimum_required(VERSION 3.13)
project(apollo_speed)

## Compile as C++11, supported in ROS Kinetic and newer
add_definitions(-std=c++11 -w)
set(CMAKE_CXX_FLAGS " -Wall -Wextra ${CMAKE_CXX_FLAGS}")
set(CMAKE_BUILD_TYPE "Release")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
find_package(IPOPT REQUIRED)
find_package(Eigen3 REQUIRED)

include_directories(
        include
        osqp/include
        ${EIGEN3_INCLUDE_DIR}
)

link_directories(osqp/lib)
add_library(${PROJECT_NAME} src/PiecewiseJerkSpeedOptimizer.cpp include/PiecewiseJerkSpeedOptimizer.hpp
        src/IpoptInterface.cpp include/IpoptInterface.hpp src/PiecewiseJerkProblem.cpp include/PiecewiseJerkProblem.hpp src/PiecewiseJerkSpeedProblem.cpp include/PiecewiseJerkSpeedProblem.hpp src/SpeedLimit.cpp include/SpeedLimit.hpp
        src/SpeedPoint.cpp include/SpeedPoint.hpp src/SpeedData.cpp include/SpeedData.hpp include/math.hpp src/PathPoint.cpp include/PathPoint.hpp src/DiscretizedPath.cpp include/DiscretizedPath.hpp src/PiecewiseJerkPathProblem.cpp include/PiecewiseJerkPathProblem.hpp src/PiecewiseJerkTrajectory1d.cpp include/PiecewiseJerkTrajectory1d.hpp include/Curve1d.hpp src/ConstantJerkTrajectory1d.cpp include/ConstantJerkTrajectory1d.hpp src/math.cpp src/PiecewiseJerkSpeedNonlinearIpoptInterface.cpp include/PiecewiseJerkSpeedNonlinearIpoptInterface.hpp src/SpeedProfileGenerator.cpp include/SpeedProfileGenerator.hpp)
target_link_libraries(${PROJECT_NAME} glog ${IPOPT_LIBRARIES} osqp
        )
add_executable(demo src/demo.cpp)
target_link_libraries(demo ${PROJECT_NAME})