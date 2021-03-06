cmake_minimum_required(VERSION 3.5)
project(weston-cmake)

set(CMAKE_MODULE_PATH
    ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake CACHE STRING
    "CMake module path." FORCE)

include_directories(${CMAKE_SOURCE_DIR})

set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
find_package(Threads REQUIRED)

set(BUILD_SHARED_LIBS ON)
set(BUILD_TESTS ON)
set(CMAKE_VERBOSE_MAKEFILE ON)
set(CMAKE_COLOR_MAKEFILE   ON)
set(ENABLE_PRECOMPILED_HEADERS OFF)

if("${CMAKE_BUILD_TYPE}" STREQUAL "Debug" OR
   "${CMAKE_BUILD_TYPE}" STREQUAL "")
    set(CONFIG_DIR "debug" CACHE STRING "Build config directory" FORCE)
elseif("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
    set(CONFIG_DIR "release" CACHE STRING "Build config directory" FORCE)
elseif("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
    set(CONFIG_DIR "releaseMinSize" CACHE STRING "Build config directory" FORCE)
elseif("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
    set(CONFIG_DIR "releaseMinSize" CACHE STRING "Build config directory" FORCE)
else()
    message(FATAL_ERROR "Invalid build type: " ${CMAKE_BUILD_TYPE})
endif()

set(BUILD_BASE_DIR ${CMAKE_SOURCE_DIR}/build CACHE STRING
        "Build root directory.")
set(CMAKE_BINARY_DIR ${BUILD_BASE_DIR}/${CONFIG_DIR} CACHE STRING
        "Build directory.")
message(STATUS "Build base directory:               ${BUILD_BASE_DIR}")
message(STATUS "Build config directory:             ${CMAKE_BINARY_DIR}")

set(OUTPUT_BASE_DIR ${CMAKE_SOURCE_DIR}/output)
message(STATUS "Output base directory:              ${OUTPUT_BASE_DIR}")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${OUTPUT_BASE_DIR}/lib)
message(STATUS "Static lib output directory:        ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${OUTPUT_BASE_DIR}/bin)
message(STATUS "Dynamic lib output directory:       ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${OUTPUT_BASE_DIR}/bin)
message(STATUS "Executable output directory:        ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")

set(PACKAGE_DOWNLOAD_DIR ${CMAKE_SOURCE_DIR}/download)
message(STATUS "Download directory:                 ${PACKAGE_DOWNLOAD_DIR}")
set(PACKAGE_BUILD_DIR ${CMAKE_SOURCE_DIR}/build)
message(STATUS "Build directory:                    ${PACKAGE_BUILD_DIR}")
set(PACKAGE_STAMP_DIR ${CMAKE_SOURCE_DIR}/stamp)
message(STATUS "Stamp directory:                    ${PACKAGE_STAMP_DIR}")
set(PACKAGE_STAGING_DIR ${CMAKE_SOURCE_DIR}/staging)

if(NOT SYSROOT_PATH)
    set(SYSROOT_PATH /home/rene/output/target)
    message(STATUS "Setting default sysroot             ${SYSROOT_PATH}")
endif()
if(NOT STAGING_PATH)
    set(STAGING_PATH /home/rene/output/staging)
    message(STATUS "Setting default staging             ${STAGING_PATH}")
endif()
set(CMAKE_SYSROOT ${SYSROOT_PATH} CACHE PATH "System root directory" FORCE)
set(CMAKE_INSTALL_PREFIX ${CMAKE_SYSROOT}/usr CACHE PATH "Install directory" FORCE)
set(CMAKE_STAGING_DIR ${STAGING_PATH} CACHE PATH "Staging directory" FORCE)
set(CMAKE_STAGING_PREFIX ${CMAKE_STAGING_DIR}/usr)
message(STATUS "Staging root directory:             ${CMAKE_STAGING_DIR}")
message(STATUS "System root directory:              ${CMAKE_SYSROOT}")
message(STATUS "Staging prefix directory:           ${CMAKE_STAGING_PREFIX}")
message(STATUS "Install prefix directory:           ${CMAKE_INSTALL_PREFIX}")

set(STAGING_INCLUDE_PATH ${CMAKE_STAGING_PREFIX}/include)
set(STAGING_LIBRARY_PATH ${CMAKE_STAGING_PREFIX}/lib)
set(STAGING_DATA_PATH ${CMAKE_STAGING_PREFIX}/share)
set(STAGING_EXECUTABLE_PATH ${CMAKE_STAGING_PREFIX}/bin)
set(STAGING_LIBEXEC_PATH ${CMAKE_STAGING_DIR}/libexec)

set(SYSTEM_INCLUDE_PATH ${CMAKE_INSTALL_PREFIX}/include)
set(SYSTEM_LIBRARY_PATH ${CMAKE_INSTALL_PREFIX}/lib)
set(SYSTEM_DATA_PATH ${CMAKE_INSTALL_PREFIX}/share)
set(SYSTEM_EXECUTABLE_PATH ${CMAKE_INSTALL_PREFIX}/bin)
set(SYSTEM_LIBEXEC_PATH ${CMAKE_SYSROOT}/libexec)

set(SYSTEM_PKG_CONFIG_PATH_LIB ${SYSTEM_LIBRARY_PATH}/pkgconfig)
set(SYSTEM_PKG_CONFIG_PATH_DATA ${SYSTEM_DATA_PATH}/pkgconfig)

set(CMAKE_PKG_CONFIG_PATH "${SYSTEM_PKG_CONFIG_PATH_LIB}:${SYSTEM_PKG_CONFIG_PATH_DATA}" CACHE PATH "pkg-config directory" FORCE)
set(ENV{PKG_CONFIG_PATH} ${CMAKE_PKG_CONFIG_PATH})
set(SYSTEM_PKG_CONFIG_PATH ${CMAKE_PKG_CONFIG_PATH})

message(STATUS "Staging include path                ${STAGING_INCLUDE_PATH}")
message(STATUS "Staging libraries path              ${STAGING_LIBRARY_PATH}")
message(STATUS "Staging data path                   ${STAGING_DATA_PATH}")
message(STATUS "Staging binary path                 ${STAGING_EXECUTABLE_PATH}")
message(STATUS "Staging libexec path                ${STAGING_LIBEXEC_PATH}")

message(STATUS "System include path                 ${SYSTEM_INCLUDE_PATH}")
message(STATUS "System libraries path               ${SYSTEM_LIBRARY_PATH}")
message(STATUS "System data path                    ${SYSTEM_DATA_PATH}")
message(STATUS "System binary path                  ${SYSTEM_EXECUTABLE_PATH}")
message(STATUS "System libexec path                 ${SYSTEM_LIBEXEC_PATH}")
message(STATUS "System pkg-config lib path          ${SYSTEM_PKG_CONFIG_PATH_LIB}")
message(STATUS "System pkg-config data path         ${SYSTEM_PKG_CONFIG_PATH_DATA}")
message(STATUS "System pkg-config path              ${SYSTEM_PKG_CONFIG_PATH}")

add_custom_target(install-components)

add_subdirectory(package)
