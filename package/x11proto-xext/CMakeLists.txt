project(x11proto-xext)

set(PROJECT_TARGET_NAME xextproto)
# Do not rename this to PROJECT_SOURCE_DIR, as this is a cmake reserved name!
set(PROJECT_SOURCE_LOCATION ${CMAKE_SOURCE_DIR}/source/${PROJECT_TARGET_NAME}-7.3.0)

set(${PROJECT_NAME}_export_pkg_config
    ${PROJECT_SOURCE_LOCATION}/${PROJECT_TARGET_NAME}.pc)
set(${PROJECT_NAME}_export_headers
    ${PROJECT_SOURCE_LOCATION}/shapestr.h
    ${PROJECT_SOURCE_LOCATION}/shmstr.h
    ${PROJECT_SOURCE_LOCATION}/syncstr.h
    ${PROJECT_SOURCE_LOCATION}/dpmsconst.h
    ${PROJECT_SOURCE_LOCATION}/dpmsproto.h
    ${PROJECT_SOURCE_LOCATION}/ge.h
    ${PROJECT_SOURCE_LOCATION}/geproto.h
    ${PROJECT_SOURCE_LOCATION}/lbx.h
    ${PROJECT_SOURCE_LOCATION}/lbxproto.h
    ${PROJECT_SOURCE_LOCATION}/mitmiscconst.h
    ${PROJECT_SOURCE_LOCATION}/mitmiscproto.h
    ${PROJECT_SOURCE_LOCATION}/multibufconst.h
    ${PROJECT_SOURCE_LOCATION}/multibufproto.h
    ${PROJECT_SOURCE_LOCATION}/secur.h
    ${PROJECT_SOURCE_LOCATION}/securproto.h
    ${PROJECT_SOURCE_LOCATION}/shapeconst.h
    ${PROJECT_SOURCE_LOCATION}/shapeproto.h
    ${PROJECT_SOURCE_LOCATION}/shm.h
    ${PROJECT_SOURCE_LOCATION}/shmproto.h
    ${PROJECT_SOURCE_LOCATION}/syncconst.h
    ${PROJECT_SOURCE_LOCATION}/syncproto.h
    ${PROJECT_SOURCE_LOCATION}/ag.h
    ${PROJECT_SOURCE_LOCATION}/agproto.h
    ${PROJECT_SOURCE_LOCATION}/cup.h
    ${PROJECT_SOURCE_LOCATION}/cupproto.h
    ${PROJECT_SOURCE_LOCATION}/dbe.h
    ${PROJECT_SOURCE_LOCATION}/dbeproto.h
    ${PROJECT_SOURCE_LOCATION}/EVI.h
    ${PROJECT_SOURCE_LOCATION}/EVIproto.h
    ${PROJECT_SOURCE_LOCATION}/xtestext1proto.h
    ${PROJECT_SOURCE_LOCATION}/xtestext1const.h
    ${PROJECT_SOURCE_LOCATION}/xtestconst.h
    ${PROJECT_SOURCE_LOCATION}/xtestproto.h)
set(${PROJECT_NAME}_export_doc
    ${PROJECT_SOURCE_LOCATION}/specs/appgrp.xml
    ${PROJECT_SOURCE_LOCATION}/specs/dbe.xml
    ${PROJECT_SOURCE_LOCATION}/specs/dpms.xml
    ${PROJECT_SOURCE_LOCATION}/specs/evi.xml
    ${PROJECT_SOURCE_LOCATION}/specs/geproto.xml
    ${PROJECT_SOURCE_LOCATION}/specs/lbx.xml
    ${PROJECT_SOURCE_LOCATION}/specs/multibuf.xml
    ${PROJECT_SOURCE_LOCATION}/specs/security.xml
    ${PROJECT_SOURCE_LOCATION}/specs/shape.xml
    ${PROJECT_SOURCE_LOCATION}/specs/shm.xml
    ${PROJECT_SOURCE_LOCATION}/specs/sync.xml
    ${PROJECT_SOURCE_LOCATION}/specs/tog-cup.xml
    ${PROJECT_SOURCE_LOCATION}/specs/xtest.xml
    ${PROJECT_SOURCE_LOCATION}/specs/appendix.xml)

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

