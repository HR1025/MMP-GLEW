cmake_minimum_required(VERSION 3.8)

project(GLEW)

if (USE_OPENGL)
    set(GLEW_SRCS)
    set(GLEW_INCS)
    set(GLEW_LIB_DIR)
    set(GLEW_LIBS)

    if (NOT OPENGL_FOUND) # 交叉编译时需外部设置
        if (CMAKE_HOST_UNIX)
            set(OpenGL_GL_PREFERENCE GLVND)
        endif()
        find_package(OpenGL REQUIRED)
    endif()
    message("-- Found OPENGL")
    message("  -- OpenGL INCS : ${OPENGL_INCLUDE_DIR}")
    message("  -- OpenGL LIB DIR : ${OpenGL_LIBRARY_DIRS}")
    message("  -- OpenGL LIBS : ${OPENGL_LIBRARIES}")

    # OpenGL Wrapper
    list(APPEND GLEW_INCS ${OPENGL_INCLUDE_DIR})
    list(APPEND GLEW_LIB_DIR ${OpenGL_LIBRARY_DIRS})
    list(APPEND GLEW_LIBS ${OPENGL_LIBRARIES})

    if (USE_EGL AND NOT NOT_AUTO_EGL)
        list(APPEND GLEW_LIBS EGL)
    endif()

    # GLEW
    list(APPEND GLEW_INCS ${CMAKE_CURRENT_SOURCE_DIR}/include)
    list(APPEND GLEW_SRCS
        ${CMAKE_CURRENT_SOURCE_DIR}/include/GL/glew.h
        ${CMAKE_CURRENT_SOURCE_DIR}/include/GL/glxew.h
        ${CMAKE_CURRENT_SOURCE_DIR}/include/GL/wglew.h
        ${CMAKE_CURRENT_SOURCE_DIR}/src/glew.c
    )

    add_library(GLEW STATIC ${GLEW_SRCS})
    add_library(Mmp::GLEW ALIAS GLEW)
    target_compile_definitions(GLEW PUBLIC GLEW_STATIC)
    if (USE_EGL)
        target_compile_definitions(GLEW PUBLIC GLEW_EGL)
    endif()
    target_include_directories(GLEW PUBLIC ${GLEW_INCS})
    target_link_libraries(GLEW PRIVATE ${GLEW_LIBS})
    target_compile_definitions(GLEW PUBLIC GLEW_NO_GLU)
endif()