project(x11proto-xf86dri)

set(PROJECT_TARGET_NAME xf86driproto)
# Do not rename this to PROJECT_SOURCE_DIR, as this is a cmake reserved name!
set(PROJECT_SOURCE_LOCATION ${CMAKE_SOURCE_DIR}/source/${PROJECT_TARGET_NAME}-2.1.1)

set(${PROJECT_NAME}_export_pkg_config
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc)
set(${PROJECT_NAME}_export_headers
    ${PROJECT_SOURCE_LOCATION}/xf86dri.h
    ${PROJECT_SOURCE_LOCATION}/xf86dristr.h
    ${PROJECT_SOURCE_LOCATION}/xf86driproto.h)
set(${PROJECT_NAME}_export_doc
    )

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
    DESTINATION "${SYSTEM_INCLUDE_PATH}/X11/dri"
    COMPONENT ${PROJECT_NAME})

