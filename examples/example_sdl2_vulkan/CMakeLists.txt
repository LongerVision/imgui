cmake_minimum_required(VERSION 3.10.2)
project(example_sdl2_vulkan)

find_package(Vulkan REQUIRED)
find_package(SDL2 REQUIRED)

add_executable(example_sdl2_vulkan main.cpp)

target_include_directories(example_sdl2_vulkan PRIVATE
    ${CMAKE_SOURCE_DIR}/../../
    ${CMAKE_SOURCE_DIR}/../../backends
)

target_link_libraries(example_sdl2_vulkan PRIVATE imgui Vulkan::Vulkan SDL2::SDL2)
