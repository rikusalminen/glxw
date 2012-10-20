cmake_minimum_required(VERSION 2.8)

project(glxw C)

find_package(PythonInterp 2.7.0 REQUIRED)
find_package(OpenGL)

option(GLXW_USE_EGL "Use EGL" OFF)
mark_as_advanced(GLXW_USE_EGL)

include_directories(${CMAKE_CURRENT_BINARY_DIR}/include)

if(OPENGL_FOUND)
    include_directories(${OPENGL_INCLUDE_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/include/GL/glcorearb.h
            COMMAND
                ${PYTHON_EXECUTABLE}
                ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
                --api opengl
                --download
                --include ${CMAKE_CURRENT_SOURCE_DIR}/include
                --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/src/glxw.c
            ${CMAKE_CURRENT_BINARY_DIR}/include/GLXW/glxw.h
        COMMAND
            ${PYTHON_EXECUTABLE}
            ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
            --api opengl
            --generate
            --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/include/GL/glcorearb.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_library(glxw STATIC ${CMAKE_CURRENT_BINARY_DIR}/src/glxw.c)
endif()

find_path(GLX_INCLUDE_DIR GL/glx.h)
mark_as_advanced(GLX_INCLUDE_DIR)
if(GLX_INCLUDE_DIR AND NOT GLXW_USE_EGL)
    include_directories(${GLX_INCLUDE_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/include/GL/glxext.h
            COMMAND
                ${PYTHON_EXECUTABLE}
                ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
                --api glx
                --download
                --include ${CMAKE_CURRENT_SOURCE_DIR}/include
                --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/src/glxw_glx.c
            ${CMAKE_CURRENT_BINARY_DIR}/include/GLXW/glxw_glx.h
        COMMAND
            ${PYTHON_EXECUTABLE}
            ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
            --api glx
            --generate
            --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/include/GL/glxext.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_library(glxw_glx STATIC ${CMAKE_CURRENT_BINARY_DIR}/src/glxw_glx.c)
endif()

find_path(GLX_INCLUDE_DIR GL/wgl.h)
mark_as_advanced(WGL_INCLUDE_DIR)
if(WGL_INCLUDE_DIR AND NOT GLXW_USE_EGL)
    include_directories(${WGL_INCLUDE_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/include/GL/wglext.h
            COMMAND
                ${PYTHON_EXECUTABLE}
                ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
                --api wgl
                --download
                --include ${CMAKE_CURRENT_SOURCE_DIR}/include
                --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/src/glxw_wgl.c
            ${CMAKE_CURRENT_BINARY_DIR}/include/GLXW/glxw_wgl.h
        COMMAND
            ${PYTHON_EXECUTABLE}
            ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
            --api wgl
            --generate
            --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/include/GL/wglext.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_library(glxw_wgl STATIC ${CMAKE_CURRENT_BINARY_DIR}/src/glxw_wgl.c)
endif()

if(GLXW_USE_EGL)
    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/include/EGL/egl.h
            ${CMAKE_CURRENT_BINARY_DIR}/include/EGL/eglext.h
            ${CMAKE_CURRENT_BINARY_DIR}/include/EGL/eglplatform.h
            ${CMAKE_CURRENT_BINARY_DIR}/include/KHR/khrplatform.h
        COMMAND
            ${PYTHON_EXECUTABLE}
            ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
            --api egl
            --download
            --include ${CMAKE_CURRENT_SOURCE_DIR}/include
            --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_custom_command(
        OUTPUT
            ${CMAKE_CURRENT_BINARY_DIR}/src/glxw_egl.c
            ${CMAKE_CURRENT_BINARY_DIR}/include/GLXW/glxw_egl.h
        COMMAND
            ${PYTHON_EXECUTABLE}
            ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
            --api egl
            --generate
            --output ${CMAKE_CURRENT_BINARY_DIR}
        MAIN_DEPENDENCY ${CMAKE_CURRENT_SOURCE_DIR}/glxw_gen.py
        DEPENDS
            ${CMAKE_CURRENT_BINARY_DIR}/include/EGL/egl.h
            ${CMAKE_CURRENT_BINARY_DIR}/include/EGL/eglext.h
            ${CMAKE_CURRENT_BINARY_DIR}/include/EGL/eglplatform.h
            ${CMAKE_CURRENT_BINARY_DIR}/include/KHR/khrplatform.h
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR})

    add_library(glxw_egl STATIC ${CMAKE_CURRENT_BINARY_DIR}/src/glxw_egl.c)
endif()
