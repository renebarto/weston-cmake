project(x11proto-kb)

set(PROJECT_TARGET_NAME kbproto)
# Do not rename this to PROJECT_SOURCE_DIR, as this is a cmake reserved name!
set(PROJECT_SOURCE_LOCATION ${CMAKE_SOURCE_DIR}/source/${PROJECT_TARGET_NAME}-1.0.7)

set(${PROJECT_NAME}_export_pkg_config
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc)
set(${PROJECT_NAME}_export_headers
    ${PROJECT_SOURCE_LOCATION}/XKBgeom.h
    ${PROJECT_SOURCE_LOCATION}/XKB.h
    ${PROJECT_SOURCE_LOCATION}/XKBproto.h
    ${PROJECT_SOURCE_LOCATION}/XKBsrv.h
    ${PROJECT_SOURCE_LOCATION}/XKBstr.h)
set(${PROJECT_NAME}_export_doc
    ${PROJECT_SOURCE_LOCATION}/specs/xkbproto.xml
    ${PROJECT_SOURCE_LOCATION}/specs/acknowledgements.xml
    ${PROJECT_SOURCE_LOCATION}/specs/appA.xml
    ${PROJECT_SOURCE_LOCATION}/specs/appB.xml
    ${PROJECT_SOURCE_LOCATION}/specs/appC.xml
    ${PROJECT_SOURCE_LOCATION}/specs/appD.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch01.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch02.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch03.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch04.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch05.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch06.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch07.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch08.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch09.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch10.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch11.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch12.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch13.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch14.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch15.xml
    ${PROJECT_SOURCE_LOCATION}/specs/ch16.xml
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-1.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-2.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-3.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-4.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-5.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-6.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-7.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-8.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-9.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-10.svg
    ${PROJECT_SOURCE_LOCATION}/specs/XKBproto-11.svg)

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

