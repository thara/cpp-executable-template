cmake_minimum_required(VERSION 3.8)
project(cpp_executable_template)

set(CMAKE_CXX_STANDARD 17)
set(LIB_DIR lib)

include_directories(include)

set(SOURCE_FILES main.cpp src/sample.cpp include/sample.h)
add_library(cpp_executable_template $<TARGET_OBJECTS:tinytoml> ${SOURCE_FILES})
add_executable(cpp_executable_template_main main.cpp $<TARGET_OBJECTS:cmdline>)

# https://github.com/mayah/tinytoml
add_library(tinytoml OBJECT lib/tinytoml/toml.h)
include_directories(tinytoml lib/tinytoml)

# https://github.com/tanakh/cmdline
add_library(cmdline OBJECT lib/cmdline/cmdline.h)
include_directories(cmdline lib/cmdline)

if(EXISTS ${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    conan_basic_setup()
else()
    message(WARNING "The file conanbuildinfo.cmake doesn't exist, you have to run 'conan install ../' first in ${CMAKE_BINARY_DIR}")
endif()

if (APPLE)
    add_definitions(-DGTEST_USE_OWN_TR1_TUPLE)
    add_definitions(-D__GLIBCXX__)
endif (APPLE)

if(NOT without-test)
    enable_testing()

    # Download and unpack googletest at configure time
    configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
    execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
            RESULT_VARIABLE result
            WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
    if(result)
        message(FATAL_ERROR "CMake step for googletest failed: ${result}")
    endif()
    execute_process(COMMAND ${CMAKE_COMMAND} --build .
            RESULT_VARIABLE result
            WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
    if(result)
        message(FATAL_ERROR "Build step for googletest failed: ${result}")
    endif()

    # Prevent overriding the parent project's compiler/linker
    # settings on Windows
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

    # Add googletest directly to our build. This defines
    # the gtest and gtest_main targets.
    add_subdirectory(${CMAKE_BINARY_DIR}/googletest-src ${CMAKE_BINARY_DIR}/googletest-build)

    add_subdirectory(test)
endif()

target_link_libraries(cpp_executable_template ${CONAN_LIBS})
target_link_libraries(cpp_executable_template_main cpp_executable_template)