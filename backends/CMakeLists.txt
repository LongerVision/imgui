if (IMGUI_BACKEND_OPENGL2 OR IMGUI_BACKEND_OPENGL3)
  if(POLICY CMP0072)
    cmake_policy(SET CMP0072 NEW)
  endif()
  find_package(OpenGL REQUIRED)
endif()

# OpenGL 2
if (IMGUI_BACKEND_OPENGL2)
  add_library(imgui-opengl2 INTERFACE)
  target_sources(imgui-opengl2 INTERFACE
      "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_opengl2.h"
      "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_opengl2.cpp"
  )
  target_link_libraries(imgui-opengl2 INTERFACE
    imgui
    OpenGL::GL
  )
endif()

# OpenGL 3
if (IMGUI_BACKEND_OPENGL3)
  add_library(imgui-opengl3 INTERFACE)
  target_sources(imgui-opengl3 INTERFACE
      "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_opengl3.h"
      "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_opengl3.cpp"
  )
  target_link_libraries(imgui-opengl3 INTERFACE
    imgui
    OpenGL::GL
    gl3w # TODO: allow to choose OpenGL3 loader
  )
endif()

# SDL
if(IMGUI_BACKEND_SDL)
  # find SDL2
  if(CMAKE_SYSTEM_NAME STREQUAL Emscripten)
    # Nothing to do for emscripten builds. SDL is bunled with the compiler.
  elseif(TARGET SDL2::SDL2)
    # user has already added SDL2::SDL2 target by any means
  elseif(MSVC AND IMGUI_SDL2_PREBUILT_DIR)
	# On Windows you may set IMGUI_SDL2_PREBUILT_DIR variable and link to binary SDL distribution.
	if(CMAKE_SIZEOF_VOID_P EQUAL 8)
	  set(IMGUI_SDL2_PLATFORM_ARCH x64)
	elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
	  set(IMGUI_SDL2_PLATFORM_ARCH x86)
	else ()
	  message(FATAL_ERROR "Unsupported platform.")
	endif()

	add_library(SDL2 SHARED IMPORTED GLOBAL)
	target_include_directories(SDL2 INTERFACE "${IMGUI_SDL2_PREBUILT_DIR}/include")
	set_target_properties(SDL2 PROPERTIES
	  IMPORTED_LOCATION "${IMGUI_SDL2_PREBUILT_DIR}/lib/${IMGUI_SDL2_PLATFORM_ARCH}/SDL2.dll"
	  IMPORTED_IMPLIB "${IMGUI_SDL2_PREBUILT_DIR}/lib/${IMGUI_SDL2_PLATFORM_ARCH}/SDL2.lib"
	)
	add_library(SDL2::SDL2 ALIAS SDL2)

	add_library(SDL2main STATIC IMPORTED GLOBAL)
	set_target_properties(SDL2main PROPERTIES
	  IMPORTED_LOCATION "${IMGUI_SDL2_PREBUILT_DIR}/lib/${IMGUI_SDL2_PLATFORM_ARCH}/SDL2main.lib"
	)
  elseif(SDL2_FOUND)
    # Platforms that do not export target but use old CMake conventions can be handled this way.
    add_library(SDL2::SDL2 INTERFACE IMPORTED)
    target_link_libraries(SDL2::SDL2 INTERFACE ${SDL2_LIBRARIES})
    target_include_directories(SDL2::SDL2 INTERFACE ${SDL2_INCLUDE_DIRS})
  elseif(PkgConfig_FOUND)
    # Rest of the platforms (like MacOS) can consume SDL via pkg-config.
    # CMake 3.6 supports IMPORTED_TARGET parameter which creates PkgConfig::sdl2 target that we can easily link to.
    # TODO: Switch to using IMPORTED_TARGET when CMake minimal version is increased.
    pkg_check_modules(SDL2 QUIET sdl2)
    if(SDL2_FOUND)
      add_library(SDL2::SDL2 INTERFACE IMPORTED)
      target_link_libraries(SDL2::SDL2 INTERFACE ${SDL2_LDFLAGS})
      target_compile_options(SDL2::SDL2 INTERFACE ${SDL2_CFLAGS})
    endif()
  else()
    find_package(SDL2 REQUIRED)
  endif()

  add_library(imgui-sdl INTERFACE)
  target_sources(imgui-sdl INTERFACE
    "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_sdl2.h"
    "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_sdl2.cpp"
  )
  target_include_directories(imgui-sdl INTERFACE "${CMAKE_CURRENT_LIST_DIR}")
  target_link_libraries(imgui-sdl INTERFACE imgui SDL2::SDL2)
endif()

# GLFW
if(IMGUI_BACKEND_GLFW)
  # find glfw
  if(TARGET glfw)
    # GLFW exists already. Nothing to do.
  elseif(MSVC)
    # On Windows you may set IMGUI_GLFW_PREBUILT_DIR variable and link to binary GLFW distribution.
    if(IMGUI_GLFW_PREBUILT_DIR)
	  set(IMGUI_GLFW_INCLUDE_DIR "${IMGUI_GLFW_PREBUILT_DIR}/include")
	  set(IMGUI_GLFW_LIB_DIR "${IMGUI_GLFW_PREBUILT_DIR}/lib-vc2019") # TODO: don't hardcode VC version

	  add_library(glfw SHARED IMPORTED GLOBAL)
	  target_include_directories(glfw INTERFACE "${IMGUI_GLFW_INCLUDE_DIR}")
	  set_target_properties(glfw PROPERTIES
		IMPORTED_IMPLIB   "${IMGUI_GLFW_LIB_DIR}/glfw3.lib"
	    IMPORTED_LOCATION "${IMGUI_GLFW_LIB_DIR}/glfw3.dll"
	  )
	else() # Use prebuilt GLFW from Dear ImGui's repo (only static build is included)
      if(CMAKE_SIZEOF_VOID_P EQUAL 8)
        set(IMGUI_GLFW_PLATFORM_BITS 64)
      elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
        set(IMGUI_GLFW_PLATFORM_BITS 32)
      else()
        message(FATAL_ERROR "Unsupported platform.")
      endif()
	  set(IMGUI_GLFW_DIR "${PROJECT_SOURCE_DIR}/examples/libs/glfw")
	  set(IMGUI_GLFW_INCLUDE_DIR "${IMGUI_GLFW_DIR}/include")
	  set(IMGUI_GLFW_LIB_DIR "${IMGUI_GLFW_DIR}/lib-vc2010-${IMGUI_GLFW_PLATFORM_BITS}")

	  add_library(glfw STATIC IMPORTED GLOBAL)
	  target_include_directories(glfw INTERFACE "${IMGUI_GLFW_INCLUDE_DIR}")
	  set_target_properties(glfw PROPERTIES
		IMPORTED_LOCATION "${IMGUI_GLFW_LIB_DIR}/glfw3.lib"
	  )
	endif()
  elseif(PkgConfig_FOUND)
    # CMake 3.6 supports IMPORTED_TARGET parameter which creates PkgConfig::glfw target that we can easily link to.
    # TODO: Switch to using IMPORTED_TARGET when CMake minimal version is increased.
    pkg_check_modules(GLFW QUIET glfw3)
    if(GLFW_FOUND)
      add_library(glfw INTERFACE IMPORTED)
      target_link_libraries(glfw INTERFACE ${GLFW_LDFLAGS})
      target_compile_options(glfw INTERFACE ${GLFW_CFLAGS})
    endif()
  else()
    find_package(glfw3 REQUIRED)
  endif()

#  add_library(glfw INTERFACE)
#  target_sources(imgui-glfw INTERFACE
#    "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_glfw.h"
#    "${CMAKE_CURRENT_LIST_DIR}/imgui_impl_glfw.cpp"
#  )
#  target_link_libraries(imgui-glfw INTERFACE ${CMAKE_DL_LIBS} glfw)
#  target_include_directories(imgui-glfw INTERFACE "${CMAKE_CURRENT_LIST_DIR}")
endif()
