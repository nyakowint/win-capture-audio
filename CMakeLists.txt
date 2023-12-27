cmake_minimum_required(VERSION 3.20)
project(win-capture-audio VERSION 2.2.3)

set(PLUGIN_AUTHOR "bozbez")
set(RELEASE_DIR "${PROJECT_SOURCE_DIR}/release")

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set(ARCH_NAME "64bit")
else()
    set(ARCH_NAME "32bit")
endif()

configure_file(
    installer/installer.iss.in
    ../installer/installer.generated.iss
)

execute_process(
    COMMAND git log -1 --format=%h
        WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
        OUTPUT_VARIABLE GIT_HASH
        OUTPUT_STRIP_TRAILING_WHITESPACE
)

configure_file(
    src/plugin-macros.hpp.in
    src/plugin-macros.generated.hpp
)

set(win-capture-audio_SOURCES
    src/plugin.cpp
    src/audio-capture.cpp
    src/audio-capture-helper.cpp
    src/session-monitor.cpp
    src/mixer.cpp)

add_library(win-capture-audio MODULE ${win-capture-audio_SOURCES})
target_link_libraries(win-capture-audio libobs dwmapi psapi ksuser mmdevapi mfplat)

set_property(TARGET win-capture-audio PROPERTY CXX_STANDARD 23)
target_include_directories(win-capture-audio PUBLIC deps/wil/include)

# cmake-format: off
set_target_properties_obs(win-capture-audio PROPERTIES FOLDER plugins PREFIX "")
# cmake-format: on