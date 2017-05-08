project(x11proto-x)

set(PROJECT_TARGET_NAME xproto)
# Do not rename this to PROJECT_SOURCE_DIR, as this is a cmake reserved name!
set(PROJECT_SOURCE_LOCATION ${CMAKE_SOURCE_DIR}/source/${PROJECT_TARGET_NAME}-7.0.31)

set(${PROJECT_NAME}_export_pkg_config
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc)
set(${PROJECT_NAME}_export_headers
    ${PROJECT_SOURCE_LOCATION}/Xfuncproto.h
    ${PROJECT_SOURCE_LOCATION}/Xpoll.h
    ${PROJECT_SOURCE_LOCATION}/ap_keysym.h
    ${PROJECT_SOURCE_LOCATION}/DECkeysym.h
    ${PROJECT_SOURCE_LOCATION}/HPkeysym.h
    ${PROJECT_SOURCE_LOCATION}/keysymdef.h
    ${PROJECT_SOURCE_LOCATION}/keysym.h
    ${PROJECT_SOURCE_LOCATION}/Sunkeysym.h
    ${PROJECT_SOURCE_LOCATION}/Xalloca.h
    ${PROJECT_SOURCE_LOCATION}/Xarch.h
    ${PROJECT_SOURCE_LOCATION}/Xatom.h
    ${PROJECT_SOURCE_LOCATION}/Xdefs.h
    ${PROJECT_SOURCE_LOCATION}/XF86keysym.h
    ${PROJECT_SOURCE_LOCATION}/Xfuncs.h
    ${PROJECT_SOURCE_LOCATION}/X.h
    ${PROJECT_SOURCE_LOCATION}/Xmd.h
    ${PROJECT_SOURCE_LOCATION}/Xosdefs.h
    ${PROJECT_SOURCE_LOCATION}/Xos.h
    ${PROJECT_SOURCE_LOCATION}/Xos_r.h
    ${PROJECT_SOURCE_LOCATION}/Xproto.h
    ${PROJECT_SOURCE_LOCATION}/Xprotostr.h
    ${PROJECT_SOURCE_LOCATION}/Xthreads.h
    ${PROJECT_SOURCE_LOCATION}/Xw32defs.h
    ${PROJECT_SOURCE_LOCATION}/XWDFile.h
    ${PROJECT_SOURCE_LOCATION}/Xwindows.h
    ${PROJECT_SOURCE_LOCATION}/Xwinsock.h)
set(${PROJECT_NAME}_export_doc
    ${PROJECT_SOURCE_LOCATION}/specs/x11protocol.xml
    ${PROJECT_SOURCE_LOCATION}/specs/encoding.xml
    ${PROJECT_SOURCE_LOCATION}/specs/glossary.xml
    ${PROJECT_SOURCE_LOCATION}/specs/keysyms.xml
    ${PROJECT_SOURCE_LOCATION}/specs/sect1-9.xml)

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
configure_file(
    ${PROJECT_SOURCE_LOCATION}/Xpoll.h.in
    ${PROJECT_SOURCE_LOCATION}/Xpoll.h
    @ONLY)
configure_file(
    ${PROJECT_SOURCE_LOCATION}/do-not-use-config.h.in
    ${PROJECT_SOURCE_LOCATION}/do-not-use-config.h
    @ONLY)
configure_file(
    ${PROJECT_SOURCE_LOCATION}/Xfuncproto.h.in
    ${PROJECT_SOURCE_LOCATION}/Xfuncproto.h
    @ONLY)
install(FILES ${${PROJECT_NAME}_export_pkg_config}
    DESTINATION "${SYSTEM_PKG_CONFIG_PATH_LIB}"
    COMPONENT ${PROJECT_NAME})
install(FILES ${${PROJECT_NAME}_export_doc}
    DESTINATION "${SYSTEM_DATA_PATH}/doc/${PROJECT_TARGET_NAME}"
    COMPONENT ${PROJECT_NAME})
install(FILES ${${PROJECT_NAME}_export_headers}
    DESTINATION "${SYSTEM_INCLUDE_PATH}/X11"
    COMPONENT ${PROJECT_NAME})

