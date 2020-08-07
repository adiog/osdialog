cmake_minimum_required(VERSION 3.0)

set(CMAKE_C_STANDARD 11)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

if(UNIX AND NOT APPLE)
    set(LINUX ON)
endif()

set(sources ${CMAKE_CURRENT_SOURCE_DIR}/osdialog.c)
set(headers ${CMAKE_CURRENT_SOURCE_DIR}/osdialog.h)

set(CMAKE_C_FLAGS "-Wall -Wextra -std=c99 -pedantic")

option(HAVE_GTK2 ON)

if(LINUX)
    if(HAVE_GTK2 OR NOT HAVE_GTK3)
        find_package(GTK2 REQUIRED gtk)
        if(GTK2_FOUND)
            include_directories(${GTK2_INCLUDE_DIRS})
            add_library(osdialog ${sources} ${headers} ${CMAKE_CURRENT_SOURCE_DIR}/osdialog_gtk2.c)
            target_link_libraries(osdialog ${GTK2_LIBRARIES})
        endif()
    endif()

    if(HAVE_GTK3 AND NOT HAVE_GTK2)
        FIND_PACKAGE(PkgConfig REQUIRED)
        PKG_CHECK_MODULES(GTK3 REQUIRED gtk+-3.0)
        if(GTK3_FOUND)
            include_directories(${GTK3_INCLUDE_DIRS})
            add_library(osdialog ${sources} ${headers} ${CMAKE_CURRENT_SOURCE_DIR}/osdialog_gtk3.c)
            target_link_libraries(osdialog ${GTK3_LIBRARIES})
        endif()
    endif()
endif()

if(APPLE)
    add_library(osdialog ${sources} ${headers} ${CMAKE_CURRENT_SOURCE_DIR}/osdialog_mac.m)
    target_link_libraries(osdialog "-framework AppKit")
endif()

if(WIN32)
    add_library(osdialog ${sources} ${headers} ${CMAKE_CURRENT_SOURCE_DIR}/osdialog_mac.m)
    target_link_libraries(osdialog comdlg32)
endif()
