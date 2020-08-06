cmake_minimum_required(VERSION 3.0)

set(CMAKE_C_STANDARD 11)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

if(UNIX AND NOT APPLE)
    set(LINUX ON)
endif()

set(sources ${CMAKE_CURRENT_SOURCE_DIR}/osdialog.c)
set(headers ${CMAKE_CURRENT_SOURCE_DIR}/osdialog.h)

set(CMAKE_C_FLAGS "-Wall -Wextra -std=c99 -pedantic")

if(LINUX)
    include_directories(/usr/include/gtk-2.0)
    include_directories(/usr/include/glib-2.0/)
    include_directories(/usr/lib/x86_64-linux-gnu/glib-2.0/include/)
    include_directories(/usr/include/)
    include_directories(/usr/include/cairo/)
    include_directories(/usr/include/pango-1.0/)
    include_directories(/usr/lib/x86_64-linux-gnu/gtk-2.0/include/)
    include_directories(/usr/include/gdk-pixbuf-2.0/)
    include_directories(/usr/include/atk-1.0)

    if(HAVE_GTK2)
        find_package(GTK2 REQUIRED gtk)
        if(GTK2_FOUND)
            include_directories(${GTK2_INCLUDE_DIRS})
            add_library(osdialog ${sources} ${headers} ${CMAKE_CURRENT_SOURCE_DIR}/osdialog_gtk2.c)
            target_link_libraries(osdialog ${GTK2_LIBRARIES})
        endif()
    endif()

    if(HAVE_GTK3)
        add_library(osdialog ${sources} ${headers} ${CMAKE_CURRENT_SOURCE_DIR}/osdialog_gtk2.c)
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
