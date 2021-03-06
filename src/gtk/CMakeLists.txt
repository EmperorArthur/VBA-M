#Do not use this file directly.  Always use the top level CMakeLists.txt file


#GTK dependencies
FIND_PACKAGE ( PkgConfig REQUIRED )
FIND_PACKAGE ( Gettext REQUIRED )

# These dependencies require pkg-config to be found
PKG_CHECK_MODULES ( GTKMM REQUIRED gtkmm-2.4 )
PKG_CHECK_MODULES ( GDKMM REQUIRED gdkmm-2.4 )
PKG_CHECK_MODULES ( GLIBMM REQUIRED glibmm-2.4 )
PKG_CHECK_MODULES ( GIOMM REQUIRED giomm-2.4 )
PKG_CHECK_MODULES ( GTKGLMM REQUIRED gtkglextmm-x11-1.2 )

#Make sure the gtk ui elements are available for out of tree builds
#See window.cpp:1544 (the sGetUiFilePath function) for more details
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/ui/ DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/ui/)

SET(SRC_GTK
    configfile.cpp
    main.cpp
    system.cpp
    windowcallbacks.cpp
    filters.cpp
    generalconfig.cpp
    gameboyconfig.cpp
    gameboyadvanceconfig.cpp
    cheatlist.cpp
    cheatedit.cpp
    gameboyadvancecheatlist.cpp
    gameboycheatlist.cpp
    joypadconfig.cpp
    directoriesconfig.cpp
    displayconfig.cpp
    soundconfig.cpp
    screenarea.cpp
    screenarea-cairo.cpp
    screenarea-opengl.cpp
    tools.cpp
    window.cpp
    ../sdl/inputSDL.cpp
)

INCLUDE_DIRECTORIES(
    ${GLIBMM_INCLUDE_DIRS}
    ${GIOMM_INCLUDE_DIRS}
    ${GTKMM_INCLUDE_DIRS}
    ${GDKMM_INCLUDE_DIRS}
    ${GTKGLMM_INCLUDE_DIRS}
)

LINK_DIRECTORIES(
    ${GLIBMM_LIBRARY_DIRS}
    ${GIOMM_LIBRARY_DIRS}
    ${GTKMM_LIBRARY_DIRS}
    ${GTKGLMM_LIBRARY_DIRS}
)

ADD_EXECUTABLE (
    gvbam
    WIN32
    MACOSX_BUNDLE
    ${SRC_GTK}
)

TARGET_LINK_LIBRARIES (
    gvbam
    ${VBAMCORE_LIBS}
    ${GTKMM_LIBRARIES}
    ${GTKGLMM_LIBRARIES}
)

INSTALL(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/ui DESTINATION ${DATA_INSTALL_DIR})
IF(NOT WIN32 AND NOT APPLE)
    INSTALL(FILES ${CMAKE_CURRENT_SOURCE_DIR}/gvbam.desktop DESTINATION share/applications)
ENDIF(NOT WIN32 AND NOT APPLE)

if(APPLE)
    # this should set ROM file types correctly
    SET_PROPERTY(TARGET gvbam APPEND PROPERTY MACOSX_BUNDLE_INFO_PLIST ${CMAKE_CURRENT_SOURCE_DIR}/wxplist.in)
endif(APPLE)

#Install the executable
INSTALL(PROGRAMS ${PROJECT_BINARY_DIR}/gvbam${CMAKE_EXECUTABLE_SUFFIX} DESTINATION bin)
