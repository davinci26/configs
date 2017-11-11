project(PROJECT_NAME)
# Options. Turn on with 'cmake -Dmyvarname=ON'.
option(test "Build all tests." OFF) # Makes boolean 'test' available.
cmake_minimum_required(VERSION 2.8.8)
set(CMAKE_BUILD_TYPE Debug)
set (CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_FLAGS "-g -Wall")

set(Boost_INCLUDE_DIR {boostIncDir})
set(Boost_LIBRARY_DIR {boostLibDir})
# Find Boost
set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)

#LINK_DIRECTORIES(${Boost_LIBRARY_DIR} ${PROJECT_SOURCE_DIR}/Libraries/...)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
include_directories(${PROJECT_SOURCE_DIR}/src ${PROJECT_SOURCE_DIR}/Incl)
file(GLOB_RECURSE sources src/*.cpp src/*.c src/*.hpp src/*.h)

################################
# Normal Libraries & Executables
################################
if(Boost_FOUND)
    include_directories(${Boost_INCLUDE_DIRS})
    target_link_libraries(project_lib)
endif()
add_library(project_lib ${sources})
add_executable(project_exec mains/main.cpp ${sources})
target_link_libraries(project_exec project_lib)
################################
# Testing
################################
if (test)
if (APPLE)
  add_definitions(-DGTEST_USE_OWN_TR1_TUPLE)
  add_definitions(-D__GLIBCXX__)
endif (APPLE)

# This adds another subdirectory, which has 'project(gtest)'.
add_subdirectory(lib/gtest-1.6.0)

enable_testing()

# Include the gtest library. gtest_SOURCE_DIR is available due to
# 'project(gtest)' above.
include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

##############
# Unit Tests
##############
add_executable(runUnitTests mains/main_testing.cpp)
# Standard linking to gtest stuff.
target_link_libraries(runUnitTests gtest gtest_main)
# Extra linking for the project.
target_link_libraries(runUnitTests project_lib)
endif()
