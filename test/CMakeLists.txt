# Description : Definition of all source and header files of one package.
#               To add this package to a project, include this file in the corresponding CMakeLists.txt file.
#      Author :
#     Release :
#   Copyright : 
#    Revision : 
# ------------------------------------------------------------------------------------------------
#     History : 
# ================================================================================================
cmake_minimum_required (VERSION 3.7)
project(TestBraveCoverage)

# add definitions
add_definitions(-fprofile-arcs -ftest-coverage)

# Include diretories
include_directories(/usr/include)
link_directories(/usr/lib)

# Set the compiler options
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -g -O0 -Wall -fprofile-arcs -ftest-coverage")
set(CMAKE_CXX_OUTPUT_EXTENSION_REPLACE ON)

# Create OBJECT_DIR variable
set(OBJECT_DIR ${CMAKE_BINARY_DIR}/CMakeFiles/TestBraveCoverage.dir/src)
message("-- Object files will be output to: ${OBJECT_DIR}")

# Find required packages
find_package(GTest REQUIRED)
include_directories(${GTEST_INCLUDE_DIRS})

# Set the sources
set(sources
                src/test_main.cpp
                src/test_brave_coverage.cpp
                ../src/brave_coverage.cpp
                )
# Set Include directories
include_directories(
                src/
                ../src/
                )

# Create the executable
add_executable(TestBraveCoverage ${sources})

# Create the gcov target. Run coverage tests with 'make gcov'
add_custom_target(coverage
    COMMAND mkdir -p gcoverage
    COMMAND ${CMAKE_MAKE_PROGRAM} test
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    COMMAND mkdir -p lcoverage
    )
add_custom_command(TARGET coverage
    COMMAND echo "=================== GCOV ===================="
    COMMAND gcov -b ${CMAKE_SOURCE_DIR}/src/*.cpp -o ${OBJECT_DIR}
    COMMAND echo "-- Source diretorie: ${CMAKE_SOURCE_DIR}/src/"
    COMMAND echo "-- Coverage files have been output to ${CMAKE_BINARY_DIR}/gcoverage"
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/gcoverage

    COMMAND echo "=================== LCOV ===================="
    COMMAND echo "-- Passing lcov tool under code coverage"
    COMMAND lcov --capture --directory ../ --output-file lcoverage/main_coverage_all.info
    COMMAND lcov -r lcoverage/main_coverage_all.info "**/test/**" "**/include/**"  -o lcoverage/main_coverage_filtered.info
    COMMAND echo "-- Generating HTML output files"
    COMMAND genhtml lcoverage/main_coverage_filtered.info --output-directory lcoverage

    COMMAND ${CMAKE_MAKE_PROGRAM} clean
    COMMAND rm -f ${OBJECT_DIR}/*.gcno
    COMMAND rm -f ${OBJECT_DIR}/*.gcda
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})

add_dependencies(coverage TestBraveCoverage)


# Make sure to clean up the coverage folder
set_property(DIRECTORY APPEND PROPERTY ADDITIONAL_MAKE_CLEAN_FILES gcoverage)

# Testing
enable_testing()

add_test(output_test ${CMAKE_CURRENT_BINARY_DIR}/TestBraveCoverage)

# link Boost libraries to the new target
target_link_libraries(TestBraveCoverage gtest)
target_link_libraries(TestBraveCoverage gmock)
target_link_libraries(TestBraveCoverage pthread)
target_link_libraries(TestBraveCoverage -fprofile-arcs)
#target_link_libraries(TestBraveCoverage gcov)