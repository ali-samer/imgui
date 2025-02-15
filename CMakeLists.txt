cmake_minimum_required (VERSION 3.2)
project (ImGui)

find_package(OpenGL REQUIRED)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (WIN32)
else (WIN32)
    find_package(PkgConfig REQUIRED)
    pkg_search_module(GLFW REQUIRED glfw3)
endif (WIN32)

find_package(Threads REQUIRED)

if (APPLE)
    find_library(COREFOUNDATION_LIBRARY CoreFoundation REQUIRED)
    find_library(COCOA_LIBRARY Cocoa REQUIRED)
    find_library(IOKIT_LIBRARY IOKit REQUIRED)
else (APPLE)
    unset(COREFOUNDATION_LIBRARY)
    set(GLFW_DEPS X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL dl pthread)
endif (APPLE)

if (WIN32)
    set(GLFW_INCLUDE_DIRS "C:/glfw3/include" CACHE STRING "GLFW Includes")
    unset(GLFW_LDFLAGS)
    set(GLFW_DEPS glfw3)
endif (WIN32)

set(IMGUI_LIBRARIES ImGui CACHE STRING "ImGui Libraries")
set(IMGUI_INCLUDE_DIRS ${PROJECT_SOURCE_DIR}/../ ${PROJECT_SOURCE_DIR}/examples/libs/gl3w CACHE STRING "ImGui Includes")

# shared Library
add_library(ImGui SHARED
    imgui.cpp
    imgui_demo.cpp
    imgui_draw.cpp
    imgui_user.cpp
    imgui_widgets.cpp
    imgui_tables.cpp
    backends/imgui_impl_glfw.cpp
    backends/imgui_impl_opengl3.cpp
    examples/libs/gl3w/GL/gl3w.c
    )

set_target_properties(ImGui PROPERTIES POSITION_INDEPENDENT_CODE ON)

target_include_directories(ImGui PUBLIC
    ${GLFW_INCLUDE_DIRS}
    ${IMGUI_INCLUDE_DIRS}
    )

target_include_directories(ImGui PRIVATE
    ${PROJECT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}
    )

target_link_libraries(ImGui PUBLIC
    ${OPENGL_LIBRARIES}
    ${GLFW_LDFLAGS}
    ${GLFW_DEPS}
    )

target_link_libraries(ImGui PRIVATE
    ${CMAKE_THREAD_LIBS_INIT}
    ${COCOA_LIBRARY}
    ${IOKIT_LIBRARY}
    ${COREFOUNDATION_LIBRARY}
    )

if ("x${LIBRARY_INSTALL_PATH}" STREQUAL "x")
    set(LIBRARY_INSTALL_PATH "${PROJECT_SOURCE_DIR}/lib")
endif()

install(TARGETS ${IMGUI_LIBRARIES} DESTINATION ${LIBRARY_INSTALL_PATH})
