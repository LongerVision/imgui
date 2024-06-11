cmake_minimum_required(VERSION 3.10.2)
project(example_glfw_opengl3)

find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)

add_executable(example_glfw_opengl3 main.cpp)

target_include_directories(example_glfw_opengl3 PRIVATE
    ${CMAKE_SOURCE_DIR}/../../
    ${CMAKE_SOURCE_DIR}/../../backends
)

target_link_libraries(example_glfw_opengl3 PRIVATE imgui OpenGL::GL glfw)
