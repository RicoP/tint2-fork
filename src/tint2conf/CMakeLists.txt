cmake_minimum_required(VERSION 3.10)
project(tint2conf)

include( FindPkgConfig )
pkg_check_modules( X11_T2C REQUIRED x11 xcomposite xdamage xinerama xrender xrandr>=1.3 )
pkg_check_modules( GLIB2 REQUIRED glib-2.0 )
pkg_check_modules( GOBJECT2 REQUIRED gobject-2.0 )
pkg_check_modules( IMLIB2 REQUIRED imlib2 )
pkg_check_modules( GTHREAD2 REQUIRED gthread-2.0 )
pkg_check_modules( GTK3 REQUIRED gtk+-x11-3.0 )
pkg_check_modules( RSVG librsvg-2.0>=2.36.0 )

include_directories( ../util
                     ${X11_T2C_INCLUDE_DIRS}
                     ${GLIB2_INCLUDE_DIRS}
                     ${GOBJECT2_INCLUDE_DIRS}
                     ${IMLIB2_INCLUDE_DIRS}
                     ${GTHREAD2_INCLUDE_DIRS}
                     ${GTK3_INCLUDE_DIRS}
                     ${RSVG_INCLUDE_DIRS} )

set(SOURCES ../util/common.c
            ../util/bt.c
            ../util/strnatcmp.c
            ../util/cache.c
            ../util/timer.c
            ../util/test.c
            ../util/print.c
            ../util/signals.c
            ../config.c
            ../util/server.c
            ../util/strlcat.c
            ../launcher/apps-common.c
            ../launcher/icon-theme-common.c
            md4.c
            main.c
            properties.c
            properties_rw.c
            theme_view.c
            background_gui.c
            gradient_gui.c )

add_definitions( -DTINT2CONF )

option( ENABLE_RSVG "Rsvg support (launcher only)" ON )

if( ENABLE_RSVG )
  if( RSVG_FOUND )
    add_definitions( -DHAVE_RSVG )
  endif( RSVG_FOUND )
endif( ENABLE_RSVG )

link_directories( ${X11_T2C_LIBRARY_DIRS}
                  ${GLIB2_LIBRARY_DIRS}
                  ${GOBJECT2_LIBRARY_DIRS}
                  ${IMLIB2_LIBRARY_DIRS}
                  ${GTHREAD2_LIBRARY_DIRS}
                  ${GTK3_LIBRARY_DIRS}
                  ${RSVG_LIBRARY_DIRS} )
add_executable( tint2conf ${SOURCES} )
target_link_libraries( tint2conf ${X11_T2C_LIBRARIES}
                                 ${GLIB2_LIBRARIES}
                                 ${GOBJECT2_LIBRARIES}
                                 ${IMLIB2_LIBRARIES}
                                 ${GTHREAD2_LIBRARIES}
                                 ${GTK3_LIBRARIES}
                                 ${RSVG_LIBRARIES} )

add_definitions( -DINSTALL_PREFIX=\"${CMAKE_INSTALL_PREFIX}\" )
add_definitions( -DLOCALEDIR=\"${CMAKE_INSTALL_FULL_LOCALEDIR}\" )
add_definitions( -DGETTEXT_PACKAGE=\"tint2conf\" )
add_definitions( -DDGLIB_DISABLE_DEPRECATION_WARNINGS=1 )
set_target_properties( tint2conf PROPERTIES COMPILE_FLAGS "-Wall -Wpointer-arith -fno-strict-aliasing -pthread -std=c99 -Werror-implicit-function-declaration -Wno-deprecated -Wno-deprecated-declarations" )
set_target_properties( tint2conf PROPERTIES LINK_FLAGS "-pthread" )

add_subdirectory(po)

install( TARGETS tint2conf DESTINATION bin )
install( FILES tint2conf.svg DESTINATION ${CMAKE_INSTALL_DATADIR}/icons/hicolor/scalable/apps )
install( FILES tint2conf.desktop DESTINATION ${CMAKE_INSTALL_DATADIR}/applications )
install( FILES tint2conf.xml DESTINATION ${CMAKE_INSTALL_DATADIR}/mime/packages )
