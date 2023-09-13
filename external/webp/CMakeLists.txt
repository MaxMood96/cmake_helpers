# This file is part of Desktop App Toolkit,
# a set of libraries for developing nice desktop applications.
#
# For license and copyright information please follow this link:
# https://github.com/desktop-app/legal/blob/master/LEGAL

if (DESKTOP_APP_USE_PACKAGED)
    add_library(external_webp INTERFACE IMPORTED GLOBAL)
    add_library(desktop-app::external_webp ALIAS external_webp)

    find_package(PkgConfig REQUIRED)
    pkg_check_modules(LIBWEBP IMPORTED_TARGET)

    target_link_libraries(external_webp INTERFACE PkgConfig::LIBWEBP)

    add_library(external_libwebp INTERFACE IMPORTED GLOBAL)
    add_library(desktop-app::external_libwebp ALIAS external_libwebp)

    add_library(external_libwebpdemux INTERFACE IMPORTED GLOBAL)
    add_library(desktop-app::external_libwebpdemux ALIAS external_libwebpdemux)

    add_library(external_libwebpmux INTERFACE IMPORTED GLOBAL)
    add_library(desktop-app::external_libwebpmux ALIAS external_libwebpmux)

    return()
endif()

add_library(external_webp INTERFACE IMPORTED GLOBAL)
add_library(desktop-app::external_webp ALIAS external_webp)

add_library(external_webp_common INTERFACE IMPORTED GLOBAL)
add_library(desktop-app::external_webp_common ALIAS external_webp_common)

if (WIN32)
    target_include_directories(external_webp_common SYSTEM
    INTERFACE
        ${libs_loc}/libwebp/src
    )
elseif (APPLE)

else()
    target_include_directories(external_webp_common SYSTEM
    INTERFACE
        /usr/local/include
    )
endif()

add_library(external_libwebp STATIC IMPORTED GLOBAL)
add_library(desktop-app::external_libwebp ALIAS external_libwebp)
target_link_libraries(external_libwebp
INTERFACE
    desktop-app::external_webp_common
)

add_library(external_libwebpdemux STATIC IMPORTED GLOBAL)
add_library(desktop-app::external_libwebpdemux ALIAS external_libwebpdemux)
target_link_libraries(external_libwebpdemux
INTERFACE
    desktop-app::external_webp_common
)

add_library(external_libwebpmux STATIC IMPORTED GLOBAL)
add_library(desktop-app::external_libwebpmux ALIAS external_libwebpmux)
target_link_libraries(external_libwebpmux
INTERFACE
    desktop-app::external_webp_common
)

if (WIN32)
    if (build_win64)
        set(libwebp_release_folder x64)
    else()
        set(libwebp_release_folder x86)
    endif()
    set(webp_lib_loc ${libs_loc}/libwebp/out)
    set(webp_lib_loc_debug ${webp_lib_loc}/debug-static/${libwebp_release_folder}/lib)
    set(webp_lib_loc_release ${webp_lib_loc}/release-static/${libwebp_release_folder}/lib)

    set_target_properties(external_libwebp PROPERTIES
        IMPORTED_LOCATION "${webp_lib_loc_release}/libwebp.lib"
        IMPORTED_LOCATION_DEBUG "${webp_lib_loc_debug}/libwebp_debug.lib"
    )
    set_target_properties(external_libwebpdemux PROPERTIES
        IMPORTED_LOCATION "${webp_lib_loc_release}/libwebpdemux.lib"
        IMPORTED_LOCATION_DEBUG "${webp_lib_loc_debug}/libwebpdemux_debug.lib"
    )
    set_target_properties(external_libwebpmux PROPERTIES
        IMPORTED_LOCATION "${webp_lib_loc_release}/libwebpmux.lib"
        IMPORTED_LOCATION_DEBUG "${webp_lib_loc_debug}/libwebpmux_debug.lib"
    )
elseif (APPLE)

else()
    find_library(DESKTOP_APP_WEBP_LIBRARIES libwebp.a REQUIRED)
    set_target_properties(external_libwebp PROPERTIES
        IMPORTED_LOCATION "${DESKTOP_APP_WEBP_LIBRARIES}"
    )
    find_library(DESKTOP_APP_WEBPDEMUX_LIBRARIES libwebpdemux.a REQUIRED)
    set_target_properties(external_libwebpdemux PROPERTIES
        IMPORTED_LOCATION "${DESKTOP_APP_WEBPDEMUX_LIBRARIES}"
    )
    find_library(DESKTOP_APP_WEBPMUX_LIBRARIES libwebpmux.a REQUIRED)
    set_target_properties(external_libwebpmux PROPERTIES
        IMPORTED_LOCATION "${DESKTOP_APP_WEBPMUX_LIBRARIES}"
    )
endif()

target_link_libraries(external_webp
INTERFACE
    desktop-app::external_libwebp
    desktop-app::external_libwebpdemux
    desktop-app::external_libwebpmux
)
