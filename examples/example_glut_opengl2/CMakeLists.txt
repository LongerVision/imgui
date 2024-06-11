cmake_minimum_required(VERSION 3.10.2)
project(example_glut_opengl2)

find_package(OpenGL REQUIRED)
find_package(GLUT REQUIRED)

add_executable(example_glut_opengl2 main.cpp)

target_include_directories(example_glut_opengl2 PRIVATE
    ${CMAKE_SOURCE_DIR}/../../
    ${CMAKE_SOURCE_DIR}/../../backends
)

target_link_libraries(example_glut_opengl2 PRIVATE imgui OpenGL::GL GLUT::GLUT)
