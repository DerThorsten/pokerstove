cmake_minimum_required(VERSION 2.8)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-deprecated-register -O2")
# build the test 
file(GLOB test_sources *.test.cpp)
add_executable(util_tests ${test_sources})
target_link_libraries(util_tests peval gtest gtest_main)

# there are no libraries, only headers

add_test(TestUtil ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/util_tests)
