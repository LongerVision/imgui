cmake_minimum_required(VERSION 3.10.2)
project(example_null)

add_executable(example_null main.cpp)

target_include_directories(example_null PRIVATE
    ${CMAKE_SOURCE_DIR}/../../
    ${CMAKE_SOURCE_DIR}/../../backends
)

target_link_libraries(example_null PRIVATE imgui)
