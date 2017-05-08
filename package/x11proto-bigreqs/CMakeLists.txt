project(x11proto-bigreqs)

set(PROJECT_TARGET_NAME bigreqsproto)
# Do not rename this to PROJECT_SOURCE_DIR, as this is a cmake reserved name!
set(PROJECT_SOURCE_LOCATION ${CMAKE_SOURCE_DIR}/source/${PROJECT_TARGET_NAME}-1.1.2)

set(${PROJECT_NAME}_export_pkg_config
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc)
set(${PROJECT_NAME}_export_headers
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.h
    ${PROJECT_SOURCE_LOCATION}/bigreqstr.h)
set(${PROJECT_NAME}_export_doc
    ${PROJECT_SOURCE_LOCATION}/specs/bigreq.xml)

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

configure_file(${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc.in ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc @ONLY)
install(FILES ${${PROJECT_NAME}_export_pkg_config}
    DESTINATION "${SYSTEM_PKG_CONFIG_PATH_LIB}"
    COMPONENT ${PROJECT_NAME})
install(FILES ${${PROJECT_NAME}_export_doc}
    DESTINATION "${SYSTEM_DATA_PATH}/doc/${PROJECT_TARGET_NAME}"
    COMPONENT ${PROJECT_NAME})
install(FILES ${${PROJECT_NAME}_export_headers}
    DESTINATION "${SYSTEM_INCLUDE_PATH}/X11/extensions"
    COMPONENT ${PROJECT_NAME})
