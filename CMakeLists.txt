cmake_minimum_required(VERSION 2.8) # bump it to 3.12
set(CMAKE_CXX_FLAGS "-fpermissive")

set(VCPKG_TARGET_ARCHITECTURE x64)
set(VCPKG_CRT_LINKAGE static)
set(VCPKG_LIBRARY_LINKAGE static)
if(DEFINED ENV{VCPKG_DIR} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
    set(CMAKE_TOOLCHAIN_FILE  "$ENV{VCPKG_DIR}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
endif()

project(ethz_apriltag2)

find_package(Eigen3 REQUIRED)
find_package(OpenCV REQUIRED)

add_definitions(-fPIC -O3)
file(GLOB SOURCE_FILES "src/*.cc")

include_directories("include")
#library
add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} PRIVATE 
        ${OpenCV_LIBS}
        Eigen3::Eigen
    )

add_subdirectory(pybind11)

pybind11_add_module(apriltags_eth src/module.cpp)
target_link_libraries(apriltags_eth PRIVATE ${PROJECT_NAME})

set(APRILTAGS_ETH_PYLIB apriltags_eth PARENT_SCOPE)
set(APRILTAGS_ETH_PYLIB_PATH "$<TARGET_FILE:apriltags_eth>" PARENT_SCOPE)