find_package (PkgConfig)

pkg_check_modules (DAEMON_DEPS REQUIRED glib-2.0 gio-2.0 polkit-gobject-1 accountsservice)
add_definitions (${DAEMON_DEPS_CFLAGS})
link_directories (${DAEMON_DEPS_LIBRARY_DIRS})

set (DAEMON_EXEC_NAME pantheon-parental-controls-daemon)
set (EXEC_MONITOR_NAME pantheon-exec-monitor)

vala_precompile (VALA_C ${DAEMON_EXEC_NAME}
    Daemon.vala
    Process.vala
    SessionManager.vala
    SessionHandler.vala
    Interfaces.vala
    UserConfig.vala
    Server.vala
    Restriction.vala
    Restrictions/AppRestriction.vala
    Restrictions/TimeRestriction.vala
    Restrictions/WebRestriction.vala
    RestrictionController.vala
    ${CMAKE_SOURCE_DIR}/src/shared/Constants.vala
    ${CMAKE_SOURCE_DIR}/src/shared/Utils.vala
    ${CMAKE_SOURCE_DIR}/src/shared/PAM/Token.vala
    ${CMAKE_SOURCE_DIR}/src/shared/PAM/Reader.vala
    ${CMAKE_SOURCE_DIR}/src/shared/PAM/Writer.vala
PACKAGES
    posix
    accountsservice
    polkit-gobject-1
    gio-2.0
    gee-0.8
    exec-monitor
OPTIONS
    --vapidir=${CMAKE_SOURCE_DIR}/vapi/
    --thread
)

include_directories (${CMAKE_SOURCE_DIR}/lib/)
add_library (${EXEC_MONITOR_NAME} STATIC ${CMAKE_SOURCE_DIR}/lib/exec-monitor.c)

add_executable (${DAEMON_EXEC_NAME} ${VALA_C})
target_link_libraries (${DAEMON_EXEC_NAME} ${DEPS_LIBRARIES} ${EXEC_MONITOR_NAME}) 

# Installation
install (TARGETS ${DAEMON_EXEC_NAME} RUNTIME DESTINATION bin)
install (TARGETS ${EXEC_MONITOR_NAME} DESTINATION ${CMAKE_INSTALL_LIBDIR})
