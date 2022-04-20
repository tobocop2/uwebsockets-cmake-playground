cmake_minimum_required(VERSION 3.11)

IF (WIN32)
  set(CMAKE_TOOLCHAIN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/vcpkg/scripts/buildsystems/vcpkg.cmake CACHE STRING "Vcpkg toolchain file")
ENDIF()

project(Example)

set(CMAKE_CXX_STANDARD 20)

if (WIN32)
  find_package(unofficial-libuv REQUIRED)
ENDIF()

# Useful for debugging only, uncomment to view all env variables
# get_cmake_property(_variableNames VARIABLES)
# list (SORT _variableNames)
# foreach (_variableName ${_variableNames})
#     message(STATUS "${_variableName}=${${_variableName}}")
# endforeach()

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)


# Fetch uwebsockets
include(FetchContent)
FetchContent_Declare(uwebsockets GIT_REPOSITORY https://github.com/uNetworking/uWebSockets.git)
FetchContent_GetProperties(uwebsockets)
if(NOT uwebsockets_POPULATED)
  FetchContent_Populate(uwebsockets)
endif()

# Add libaries from uwebsockets
add_library(
  usockets
  ${uwebsockets_SOURCE_DIR}/uSockets/src/bsd.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/context.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/loop.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/socket.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/crypto/openssl.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/crypto/sni_tree.cpp
  ${uwebsockets_SOURCE_DIR}/uSockets/src/crypto/wolfssl.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/eventing/epoll_kqueue.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/eventing/gcd.c
  ${uwebsockets_SOURCE_DIR}/uSockets/src/eventing/libuv.c)


# Set include dir for libuv using libuv installed from cmake
if (WIN32)
  get_filename_component(LIBUV_INCLUDE_DIR "${unofficial-libuv_DIR}/../../include" ABSOLUTE)
ENDIF()

target_include_directories(usockets PUBLIC ${uwebsockets_SOURCE_DIR}/uSockets/src ${LIBUV_INCLUDE_DIR})
IF (NOT(WIN32))
  target_compile_options(usockets PUBLIC -flto -O3)
ENDIF()

target_compile_features(usockets PRIVATE cxx_std_11)
target_compile_definitions(usockets PRIVATE -DLIBUS_NO_SSL)

add_library(uwebsockets INTERFACE)
target_include_directories(uwebsockets INTERFACE ${uwebsockets_SOURCE_DIR}/src ${uwebsockets_SOURCE_DIR}/uSockets/src ${LIBUV_INCLUDE_DIR})
target_compile_features(uwebsockets INTERFACE cxx_std_20)
target_compile_definitions(uwebsockets INTERFACE -DUWS_NO_ZLIB)

if(WIN32)
  target_link_libraries(uwebsockets INTERFACE usockets unofficial::libuv::libuv)
ELSE()
  target_link_libraries(uwebsockets INTERFACE usockets)
ENDIF()

add_executable(echo_server src/echo_server.cpp)
target_link_libraries(echo_server PRIVATE uwebsockets)

add_executable(broadcast src/broadcast.cpp)
target_link_libraries(broadcast PRIVATE uwebsockets)

add_executable(broadcast_echo_server src/broadcast_echo_server.cpp)
target_link_libraries(broadcast_echo_server PRIVATE uwebsockets)
