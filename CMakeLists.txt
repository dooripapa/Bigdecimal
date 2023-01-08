cmake_minimum_required(VERSION 3.15)

set(CMAKE_CXX_STANDARD 17)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/googletest/googletest/include)
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/googletest/lib)

set(TEST_FILES
        ${PROJECT_SOURCE_DIR}/Bigdecimal.cpp
        ${PROJECT_SOURCE_DIR}/Bigdecimal.h
        test_Bigdecimal.cpp)

add_executable(main main.cpp ${TEST_FILES} )

target_link_libraries(main gtest gtest_main pthread)