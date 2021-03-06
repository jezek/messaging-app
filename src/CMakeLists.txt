set(MESSAGING_APP messaging-app)

set(messaging_app_HDRS
    audiorecorder.h
    fileoperations.h
    messagingapplication.h
    stickers-history-model.h
    stickers-pack-model.h
    )

set(messaging_app_SRCS
    audiorecorder.cpp
    fileoperations.cpp
    messagingapplication.cpp
    main.cpp
    stickers-history-model.cpp
    stickers-pack-model.cpp
    )

add_executable(${MESSAGING_APP}
    ${messaging_app_SRCS}
    )
qt5_use_modules(${MESSAGING_APP} Core DBus Gui Multimedia Qml Quick Sql Versit)

include_directories(
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${NOTIFY_INCLUDE_DIRS}
    )

target_link_libraries(${MESSAGING_APP} ${NOTIFY_LIBRARIES})

install(TARGETS ${MESSAGING_APP}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    )

# Handle i18n in the desktop file
set(DESKTOP_FILE ${MESSAGING_APP}.desktop)
configure_file(${DESKTOP_FILE}.in.in ${DESKTOP_FILE}.in)
add_custom_target(${DESKTOP_FILE} ALL
         COMMENT "Merging translations into ${DESKTOP_FILE}"
         COMMAND LC_ALL=C ${INTLTOOL_MERGE} -d -u ${CMAKE_SOURCE_DIR}/po ${DESKTOP_FILE}.in ${DESKTOP_FILE} >/dev/null
        )

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${DESKTOP_FILE}
    DESTINATION ${CMAKE_INSTALL_DATADIR}/applications
    )

install(FILES "messaging-app.url-dispatcher"
    DESTINATION ${CMAKE_INSTALL_DATADIR}/url-dispatcher/urls
    )

install(FILES "messaging-app-content.json"
    DESTINATION ${CMAKE_INSTALL_DATADIR}/content-hub/peers
    RENAME messaging-app
    )

add_subdirectory(qml)
