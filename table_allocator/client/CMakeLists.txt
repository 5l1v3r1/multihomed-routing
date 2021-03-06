#Standard stuff
cmake_minimum_required(VERSION 2.6)
project(table_allocator_client)

set(CMAKE_C_FLAGS "-O1 -Wall -std=gnu99 -g")

set (PROJECT_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
set (PROJECT_SRC_DIR ${PROJECT_SOURCE_DIR}/src)

find_library(LIBUV_LIBRARY uv)
find_library(LIBJSON_LIBRARY json-c)
find_library(LIBMNL_LIBRARY mnl)

set(SOURCE
    ${PROJECT_SRC_DIR}/table_allocator_client.c
    ${PROJECT_SRC_DIR}/table_allocator_client_netlink.c
) 

set(CPACK_GENERATOR "DEB")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Kristian Evensen <kristian.evensen@gmail.com>")
set(CPACK_DEBIAN_PACKAGE_NAME "table-allocator-client")
set(CPACK_DEBIAN_PACKAGE_VERSION "0.1-1")
set(CPACK_DEBIAN_PACKAGE_ARCHITECTURE "amd64")
set(CPACK_DEBIAN_PACKAGE_DEPENDS "libuv1, libjson-c3, libmnl0")
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "Table Allocator Client is a tool aimed to simplify the configuration of multihomed hosts, and must be used together with the Table Allocator Server. When started, the client will request a table lease from the Table Allocator Server. After the table lease has been acquired, the table will be written to stdout and the tool (by default) will move to the background. The reason for writing the table is so that routes easily can be added or moved to the correct table in for example an ifup script or dhclient-hook. The client tool takes care of adding and removing the required ip rules (rules removed when either address or interface is removed).")
INCLUDE(CPack)

if(NOT TARGET table_allocator_shared)
    add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../shared
        ${CMAKE_CURRENT_BINARY_DIR}/shared)
    include_directories("${CMAKE_CURRENT_SOURCE_DIR}/../shared/include")
endif()

include_directories("${PROJECT_INCLUDE_DIR}")
add_executable(${PROJECT_NAME} ${SOURCE})
target_link_libraries(${PROJECT_NAME} table_allocator_shared)
target_link_libraries(${PROJECT_NAME} ${LIBUV_LIBRARY})
target_link_libraries(${PROJECT_NAME} ${LIBJSON_LIBRARY})
target_link_libraries(${PROJECT_NAME} ${LIBMNL_LIBRARY})
install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION sbin)
