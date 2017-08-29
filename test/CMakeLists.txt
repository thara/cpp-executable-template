cmake_minimum_required(VERSION 2.8)
PROJECT(cpp_executable_template_test CXX)

include_directories(../include)
include_directories(${CMAKE_CURRENT_BINARY_DIR})

add_executable(cpp_executable_template_test test.cpp)
target_link_libraries(cpp_executable_template_test
        gtest
        gmock_main
        pthread
        util
        cpp_executable_template
        )

enable_testing()

add_test(
        NAME run_cpp_executable_template_test
        COMMAND $<TARGET_FILE:cpp_executable_template_test>
)