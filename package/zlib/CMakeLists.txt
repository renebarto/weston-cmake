project(zlib)

include(CheckTypeSize)
include(CheckFunctionExists)
include(CheckIncludeFile)
include(CheckCSourceCompiles)

include(show_target_properties)

check_include_file(sys/types.h HAVE_SYS_TYPES_H)
check_include_file(stdint.h    HAVE_STDINT_H)
check_include_file(stddef.h    HAVE_STDDEF_H)

check_type_size(off64_t OFF64_T)
if(HAVE_OFF64_T)
    add_definitions(-D_LARGEFILE64_SOURCE=1)
endif()

set(STD_INCLUDE_DIRS
    ${STAGING_INCLUDE_PATH}
    ${SYSTEM_INCLUDE_PATH}
    /usr/include
    /usr/local/include
    /usr/include/x86_64-linux-gnu
    /usr/local/include/x86_64-linux-gnu)

set(PROJECT_TARGET_NAME zlib)
set(PROJECT_TARGET_VERSION 1.2.11)
# Do not rename this to PROJECT_SOURCE_DIR, as this is a cmake reserved name!
set(PROJECT_SOURCE_LOCATION ${CMAKE_SOURCE_DIR}/source/${PROJECT_TARGET_NAME}-${PROJECT_TARGET_VERSION})

set(${PROJECT_NAME}_export_pkg_config
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc)
set(${PROJECT_NAME}_export_headers
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.h
    ${PROJECT_SOURCE_LOCATION}/zconf.h)
set(${PROJECT_NAME}_export_doc
    ${PROJECT_SOURCE_LOCATION}/zlib.3)
set(${PROJECT_NAME}_export_lib
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_TARGET_NAME}.so.1.2.11)
set(${PROJECT_NAME}_export_lib_static
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_TARGET_NAME}.a)
set(${PROJECT_NAME}_private_headers
    ${PROJECT_SOURCE_LOCATION}/crc32.h
    ${PROJECT_SOURCE_LOCATION}/deflate.h
    ${PROJECT_SOURCE_LOCATION}/gzguts.h
    ${PROJECT_SOURCE_LOCATION}/inffast.h
    ${PROJECT_SOURCE_LOCATION}/inffixed.h
    ${PROJECT_SOURCE_LOCATION}/inflate.h
    ${PROJECT_SOURCE_LOCATION}/inftrees.h
    ${PROJECT_SOURCE_LOCATION}/trees.h
    ${PROJECT_SOURCE_LOCATION}/zutil.h)
set(${PROJECT_NAME}_sources
    ${PROJECT_SOURCE_LOCATION}/adler32.c
    ${PROJECT_SOURCE_LOCATION}/compress.c
    ${PROJECT_SOURCE_LOCATION}/crc32.c
    ${PROJECT_SOURCE_LOCATION}/deflate.c
    ${PROJECT_SOURCE_LOCATION}/gzclose.c
    ${PROJECT_SOURCE_LOCATION}/gzlib.c
    ${PROJECT_SOURCE_LOCATION}/gzread.c
    ${PROJECT_SOURCE_LOCATION}/gzwrite.c
    ${PROJECT_SOURCE_LOCATION}/inflate.c
    ${PROJECT_SOURCE_LOCATION}/infback.c
    ${PROJECT_SOURCE_LOCATION}/inftrees.c
    ${PROJECT_SOURCE_LOCATION}/inffast.c
    ${PROJECT_SOURCE_LOCATION}/trees.c
    ${PROJECT_SOURCE_LOCATION}/uncompr.c
    ${PROJECT_SOURCE_LOCATION}/zutil.c)

if(ASM686)
    set(${PROJECT_NAME}_assembly contrib/asm686/match.S)
elseif (AMD64)
    set(${PROJECT_NAME}_assembly contrib/amd64/amd64-match.S)
endif ()

if(${PROJECT_NAME}_assembly)
    add_definitions(-DASMV)
    set_source_files_properties(${${PROJECT_NAME}_assembly} PROPERTIES LANGUAGE C COMPILE_FLAGS -DNO_UNDERLINE)
endif()

configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/uninstall.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/uninstall.cmake
    IMMEDIATE @ONLY)

add_custom_target(uninstall_${PROJECT_NAME}
    COMMAND ${CMAKE_COMMAND} -DCOMPONENT=${PROJECT_NAME} -P ${CMAKE_CURRENT_BINARY_DIR}/uninstall.cmake
    COMMENT "Uninstalling ${PROJECT_NAME}")

add_custom_target(install_${PROJECT_NAME}
    COMMAND ${CMAKE_COMMAND} -DCOMPONENT=${PROJECT_NAME} -P ${CMAKE_BINARY_DIR}/cmake_install.cmake
    COMMENT "Installing ${PROJECT_NAME}")

add_dependencies(install-components install_${PROJECT_NAME})

if(EXISTS ${PROJECT_SOURCE_LOCATION}/zconf.h)
    message(STATUS "Renaming")
    message(STATUS "    ${PROJECT_SOURCE_LOCATION}/zconf.h")
    message(STATUS "to 'zconf.h.included' because this file is included with zlib")
    message(STATUS "but CMake generates it automatically in the build directory.")
    file(RENAME ${PROJECT_SOURCE_LOCATION}/zconf.h ${PROJECT_SOURCE_LOCATION}/zconf.h.included)
endif()

configure_file(${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc.in ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc @ONLY)
configure_file(${PROJECT_SOURCE_LOCATION}/zconf.h.in ${PROJECT_SOURCE_LOCATION}/zconf.h @ONLY)
install(FILES ${${PROJECT_NAME}_export_pkg_config}
    DESTINATION "${SYSTEM_PKG_CONFIG_PATH_LIB}"
    COMPONENT ${PROJECT_NAME})
install(FILES ${${PROJECT_NAME}_export_doc}
    DESTINATION "${SYSTEM_DATA_PATH}/man/man3"
    COMPONENT ${PROJECT_NAME})
install(FILES ${${PROJECT_NAME}_export_headers}
    DESTINATION "${SYSTEM_INCLUDE_PATH}"
    COMPONENT ${PROJECT_NAME})

message(STATUS "Sources         : ${${PROJECT_NAME}_sources}")
message(STATUS "Assembly files  : ${${PROJECT_NAME}_assembly}")
message(STATUS "Private headers : ${${PROJECT_NAME}_private_headers}")
message(STATUS "Public headers  : ${${PROJECT_NAME}_export_headers}")

add_library(${PROJECT_NAME}_static STATIC ${${PROJECT_NAME}_sources})
target_include_directories(${PROJECT_NAME}_static
    PRIVATE ${PROJECT_SOURCE_LOCATION} ${STD_INCLUDE_DIRS}
    INTERFACE ${SYSTEM_INCLUDE_PATH})
set_target_properties(${PROJECT_NAME}_static PROPERTIES ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
set_target_properties(${PROJECT_NAME}_static PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
set_target_properties(${PROJECT_NAME}_static PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
set_target_properties(${PROJECT_NAME}_static PROPERTIES OUTPUT_NAME z)

add_library(${PROJECT_NAME}_dynamic SHARED ${${PROJECT_NAME}_sources})
target_include_directories(${PROJECT_NAME}_dynamic
    PRIVATE ${PROJECT_SOURCE_LOCATION} ${STD_INCLUDE_DIRS}
    INTERFACE ${SYSTEM_INCLUDE_PATH})
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES DEFINE_SYMBOL ZLIB_DLL)
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES SOVERSION 1)
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES VERSION ${PROJECT_TARGET_VERSION})
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES OUTPUT_NAME z)
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
if(NOT APPLE)
    set_target_properties(${PROJECT_NAME}_dynamic PROPERTIES LINK_FLAGS "-Wl,--version-script,\"${PROJECT_SOURCE_LOCATION}/zlib.map\"")
endif()

install(TARGETS ${PROJECT_NAME}_static ARCHIVE
    RUNTIME DESTINATION "${SYSTEM_EXECUTABLE_PATH}"
    ARCHIVE DESTINATION "${SYSTEM_LIBRARY_PATH}"
    LIBRARY DESTINATION "${SYSTEM_LIBRARY_PATH}"
    COMPONENT ${PROJECT_NAME})
install(TARGETS ${PROJECT_NAME}_dynamic LIBRARY
    RUNTIME DESTINATION "${SYSTEM_EXECUTABLE_PATH}"
    ARCHIVE DESTINATION "${SYSTEM_LIBRARY_PATH}"
    LIBRARY DESTINATION "${SYSTEM_LIBRARY_PATH}"
    COMPONENT ${PROJECT_NAME})

show_target_properties(${PROJECT_NAME}_static)

show_target_properties(${PROJECT_NAME}_dynamic)

add_dependencies(install_${PROJECT_NAME}
    ${PROJECT_NAME}_dynamic)
add_dependencies(install_${PROJECT_NAME}
    ${PROJECT_NAME}_static)

add_executable(example ${PROJECT_SOURCE_LOCATION}/test/example.c)
target_link_libraries(example ${PROJECT_NAME}_static)
target_include_directories(example
    PRIVATE ${PROJECT_SOURCE_LOCATION} ${STD_INCLUDE_DIRS}
    INTERFACE ${SYSTEM_INCLUDE_PATH})
add_test(example example)

add_executable(minigzip ${PROJECT_SOURCE_LOCATION}/test/minigzip.c)
target_link_libraries(minigzip ${PROJECT_NAME}_static)
target_include_directories(minigzip
    PRIVATE ${PROJECT_SOURCE_LOCATION} ${STD_INCLUDE_DIRS}
    INTERFACE ${SYSTEM_INCLUDE_PATH})

add_dependencies(install_${PROJECT_NAME}
    example)
add_dependencies(install_${PROJECT_NAME}
    minigzip)

if(HAVE_OFF64_T)
    add_executable(example64 ${PROJECT_SOURCE_LOCATION}/test/example.c)
    target_link_libraries(example64 zlib)
    set_target_properties(example64 PROPERTIES COMPILE_FLAGS "-D_FILE_OFFSET_BITS=64")
    add_test(example64 example64)

    add_executable(minigzip64 ${PROJECT_SOURCE_LOCATION}/test/minigzip.c)
    target_link_libraries(minigzip64 zlib)
    set_target_properties(minigzip64 PROPERTIES COMPILE_FLAGS "-D_FILE_OFFSET_BITS=64")
endif()
