cmake_minimum_required(VERSION 3.10.2)
project(example_sdl2_sdlrenderer2)

find_package(SDL2 REQUIRED)

add_executable(example_sdl2_sdlrenderer2 main.cpp)

target_include_directories(example_sdl2_sdlrenderer2 PRIVATE
    ${CMAKE_SOURCE_DIR}/../../
    ${CMAKE_SOURCE_DIR}/../../backends
)

target_link_libraries(example_sdl2_sdlrenderer2 PRIVATE imgui SDL2::SDL2)
