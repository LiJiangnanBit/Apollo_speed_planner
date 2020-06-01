cmake_minimum_required(VERSION 3.13)
project(apollo_speed)

## Compile as C++11, supported in ROS Kinetic and newer
add_definitions(-std=c++11 -w)
set(CMAKE_BUILD_TYPE "Release")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
find_package(IPOPT REQUIRED)
find_package(Eigen3 REQUIRED)

#include_directories(
#        include
#        osqp/include
#        ${EIGEN3_INCLUDE_DIR}
#)

link_directories(osqp/lib)

#add_library(${PROJECT_NAME} src/PiecewiseJerkSpeedOptimizer.cpp include/PiecewiseJerkSpeedOptimizer.hpp
#        src/IpoptInterface.cpp include/IpoptInterface.hpp src/PiecewiseJerkProblem.cpp include/PiecewiseJerkProblem.hpp src/PiecewiseJerkSpeedProblem.cpp include/PiecewiseJerkSpeedProblem.hpp src/SpeedLimit.cpp include/SpeedLimit.hpp
#        src/SpeedPoint.cpp include/SpeedPoint.hpp src/SpeedData.cpp include/SpeedData.hpp include/math.hpp src/PathPoint.cpp include/PathPoint.hpp src/DiscretizedPath.cpp include/DiscretizedPath.hpp src/PiecewiseJerkPathProblem.cpp include/PiecewiseJerkPathProblem.hpp src/PiecewiseJerkTrajectory1d.cpp include/PiecewiseJerkTrajectory1d.hpp include/Curve1d.hpp src/ConstantJerkTrajectory1d.cpp include/ConstantJerkTrajectory1d.hpp src/math.cpp src/PiecewiseJerkSpeedNonlinearIpoptInterface.cpp include/PiecewiseJerkSpeedNonlinearIpoptInterface.hpp src/SpeedProfileGenerator.cpp include/SpeedProfileGenerator.hpp)
add_library(${PROJECT_NAME})
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_11)
target_sources(${PROJECT_NAME}
        PRIVATE
        src/ConstantJerkTrajectory1d.cpp
        src/DiscretizedPath.cpp
        src/IpoptInterface.cpp
        src/math.cpp
        src/PathPoint.cpp
        src/PiecewiseJerkPathProblem.cpp
        src/PiecewiseJerkProblem.cpp
        src/PiecewiseJerkSpeedNonlinearIpoptInterface.cpp
        src/PiecewiseJerkSpeedOptimizer.cpp
        src/PiecewiseJerkSpeedProblem.cpp
        src/PiecewiseJerkTrajectory1d.cpp
        src/SpeedData.cpp
        src/SpeedLimit.cpp
        src/SpeedPoint.cpp
        src/SpeedProfileGenerator.cpp)
target_include_directories(${PROJECT_NAME}
        PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        PRIVATE
        ${EIGEN3_INCLUDE_DIR} ${IPOPT_INCLUDE_DIR} osqp/include
        )
target_link_libraries(${PROJECT_NAME}
        PRIVATE
        glog ${IPOPT_LIBRARIES} osqp
        )

add_executable(demo)
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_11)
target_sources(demo PRIVATE src/demo.cpp)
target_link_libraries(demo PRIVATE ${PROJECT_NAME})

# Install
include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME} demo
        EXPORT ${PROJECT_NAME}Config
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
export(TARGETS ${PROJECT_NAME}
        NAMESPACE ${PROJECT_NAME}::
        FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
install(EXPORT ${PROJECT_NAME}Config
        DESTINATION "${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}/cmake"
        NAMESPACE ${PROJECT_NAME}::
        )
install(DIRECTORY include/
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
#install(FILES ${CMAKE_CURRENT_LIST_DIR}/cmake/${PROJECT_NAME}Config.cmake
#        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmkae/${PROJECT_NAME})