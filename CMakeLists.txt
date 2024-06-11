# CMakeLists.txt for imgui
cmake_minimum_required(VERSION 3.10.2)
project(ImGui)

# Set CMP0072 policy to use GLVND by default
if(POLICY CMP0072)
    cmake_policy(SET CMP0072 NEW)
endif()

# Add source files
set(IMGUI_SOURCES
    imgui.cpp
    imgui_demo.cpp
    imgui_draw.cpp
    imgui_tables.cpp
    imgui_widgets.cpp
    backends/imgui_impl_glfw.cpp
    backends/imgui_impl_opengl3.cpp
    backends/imgui_impl_sdl2.cpp
    backends/imgui_impl_opengl2.cpp
    backends/imgui_impl_vulkan.cpp
    backends/imgui_impl_glut.cpp
    backends/imgui_impl_sdlrenderer2.cpp
)

# Add include directories
include_directories(${CMAKE_CURRENT_SOURCE_DIR}
                    ${CMAKE_CURRENT_SOURCE_DIR}/backends)

# Create the shared library
add_library(imgui SHARED ${IMGUI_SOURCES})

# Find packages
find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)
find_package(SDL2 REQUIRED)
find_package(GLUT REQUIRED)
find_package(Vulkan REQUIRED)

# Link necessary libraries
target_link_libraries(imgui PRIVATE OpenGL::GL glfw SDL2::SDL2 Vulkan::Vulkan GLUT::GLUT)

# Set the properties for the shared library
set_target_properties(imgui PROPERTIES
    VERSION 1.0
    SOVERSION 1
    PUBLIC_HEADER "imgui.h;backends/imgui_impl_glfw.h;backends/imgui_impl_opengl3.h;backends/imgui_impl_sdl2.h;backends/imgui_impl_opengl2.h;backends/imgui_impl_vulkan.h;backends/imgui_impl_glut.h;backends/imgui_impl_sdlrenderer2.h"
)

# Installation rules
install(TARGETS imgui
    LIBRARY DESTINATION lib
    PUBLIC_HEADER DESTINATION include/imgui
)

install(FILES
    imgui.h
    imgui_internal.h
    imstb_rectpack.h
    imstb_textedit.h
    imstb_truetype.h
    backends/imgui_impl_glfw.h
    backends/imgui_impl_opengl3.h
    backends/imgui_impl_sdl2.h
    backends/imgui_impl_opengl2.h
    backends/imgui_impl_vulkan.h
    backends/imgui_impl_glut.h
    backends/imgui_impl_sdlrenderer2.h
    DESTINATION include/imgui
)

# Add subdirectories for examples
add_subdirectory(examples/example_glfw_opengl2)
add_subdirectory(examples/example_glfw_opengl3)
add_subdirectory(examples/example_glfw_vulkan)
add_subdirectory(examples/example_glut_opengl2)
add_subdirectory(examples/example_null)
add_subdirectory(examples/example_sdl2_opengl2)
add_subdirectory(examples/example_sdl2_sdlrenderer2)
add_subdirectory(examples/example_sdl2_vulkan)
