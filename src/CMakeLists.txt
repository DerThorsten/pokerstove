cmake_minimum_required(VERSION 2.6)






project(pokerstove)

#-------------------------------------------------------------------------------------------------------------------
# check for c++ 11 support
#-------------------------------------------------------------------------------------------------------------------
include(CheckCXXCompilerFlag)

if (CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    CHECK_CXX_COMPILER_FLAG("-std=c++14" HAS_CPP14_FLAG)
    CHECK_CXX_COMPILER_FLAG("-std=c++11" HAS_CPP11_FLAG)
    if (HAS_CPP14_FLAG)
        message(status ": has c++14")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")
    elseif (HAS_CPP11_FLAG)
        message(status ": has c++11")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
    else()
        message(FATAL_ERROR "Unsupported compiler -- pokerstove  requires C++11 support!")
    endif()
endif()

#
# set up boost
#
# The goal is to make this work for linux/win/osx
if (UNIX)
    message(status "building for unix")
    set(BOOST_LIBRARYDIR "/usr/local/lib/")
elseif(CMAKE_GENERATOR STREQUAL Xcode)
    message(status ": building for xcode")
    set(BOOST_ROOT "/opt/include/")
    set(BOOST_LIBRARYDIR "/opt/local/lib")
else()
    message(status ": building for non-unix -> windows")
    set(BOOST_ROOT "E:/code/ext/boost_1_53_0/")
    set(BOOST_LIBRARYDIR "E:/code/ext/boost_1_53_0/lib32-msvc-10.0/")
endif()
set(Boost_USE_STATIC_LIBS   OFF)
set(Boost_USE_MULTITHREADED      ON)
find_package(Boost COMPONENTS program_options REQUIRED)
include_directories(${Boost_INCLUDE_DIR})
link_directories(${Boost_LIBRARY_DIRS})
message(status "** Boost Include: ${Boost_INCLUDE_DIR}")
message(status "** Boost Libraries: ${Boost_LIBRARY_DIRS}")
message(status "** Boost Libraries: ${Boost_LIBRARIES}")

# Set up gtest. This must be set up before any subdirectories are
# added which will use gtest.
add_subdirectory(ext/googletest)
find_library(gtest REQUIRED)
include_directories(${GTEST_INCLUDE_DIRS})
link_directories(${GTEST_LIBS_DIR})
add_definitions ("-fPIC")
enable_testing()

# check_function_exists does not work for builtins, so use try_compile.
try_compile(HAVE_BUILTIN_BITOPS
            "${PROJECT_BINARY_DIR}"
            "${PROJECT_SOURCE_DIR}/TestBuiltinBitops.cpp")

# Set up the configure file.
configure_file("${PROJECT_SOURCE_DIR}/Config.h.in"
               "${PROJECT_BINARY_DIR}/Config.h")

# Also search for includes in PROJECT_BINARY_DIR to find config.h.
include_directories(lib/ "${PROJECT_BINARY_DIR}")
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

add_subdirectory(lib/pokerstove/peval)
add_subdirectory(lib/pokerstove/penum)
add_subdirectory(lib/pokerstove/util)
add_subdirectory(programs/ps-colex)
add_subdirectory(programs/ps-eval)
add_subdirectory(programs/ps-lut)
