cmake_minimum_required(VERSION 3.00)
project(ODBC)
add_definitions(-DLOCKFREE_SYMBASE)

add_definitions("-DLOGGING_LEVEL_2")

if(WIN32 OR MINGW)
    add_definitions("-DWINDOWS -D_WIN32_WINNT=0x0600 -DWINVER=0x0600")
    set(plugin_file "${CMAKE_CURRENT_LIST_DIR}/PluginODBC_win.txt")
elseif(UNIX)
    add_definitions("-DLINUX -DNANODBC_ODBC_VERSION=SQL_OV_ODBC3")
    set(plugin_file "${CMAKE_CURRENT_LIST_DIR}/PluginODBC.txt")
endif()

if(ENABLEABI MATCHES 1)
    message("ENABLEABI")
    add_definitions("-D_GLIBCXX_USE_CXX11_ABI=1")
else()
    message("DISENABLEABI")
    add_definitions("-D_GLIBCXX_USE_CXX11_ABI=0")
endif()


set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -std=c++11 -fPIC -O2 -Wall")

include_directories(${PROJECT_SOURCE_DIR}/include)
include_directories(${PROJECT_SOURCE_DIR}/../include)
link_directories(${CMAKE_BINARY_DIR})

aux_source_directory(${PROJECT_SOURCE_DIR}/src  DIR_SRCS)

add_library(PluginODBC SHARED ${DIR_SRCS})


if(WIN32 OR MINGW)
    target_link_libraries(PluginODBC DolphinDB odbc32 odbccp32)
elseif(UNIX)
    target_link_libraries(PluginODBC DolphinDB odbc)
endif()

configure_file(${plugin_file} ${CMAKE_BINARY_DIR}/PluginODBC.txt COPYONLY)
