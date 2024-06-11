cmake_minimum_required(VERSION 3.10.2)
project(example_glfw_vulkan)

find_package(Vulkan REQUIRED)
find_package(glfw3 REQUIRED)

add_executable(example_glfw_vulkan main.cpp)

target_include_directories(example_glfw_vulkan PRIVATE
    ${CMAKE_SOURCE_DIR}/../../
    ${CMAKE_SOURCE_DIR}/../../backends
)

target_link_libraries(example_glfw_vulkan PRIVATE imgui Vulkan::Vulkan glfw)
