include("${CMAKE_SOURCE_DIR}/src/make/config.cmake")

project(idle CXX)

if (UNIX AND NOT APPLE)
	find_package (Qt5X11Extras REQUIRED)
endif (UNIX AND NOT APPLE)

set(SOURCES "idle.cpp")
set(HEADERS "idle.h")

if (APPLE)
	set(SOURCES ${SOURCES} "idle_mac.cpp")
elseif (UNIX)
	set(SOURCES ${SOURCES} "idle_x11.cpp")
	include(FindX11)
	if (X11_Xscreensaver_FOUND)
		add_definitions(-DHAVE_XSS)
		include_directories(AFTER "${X11_Xscreensaver_INCLUDE_PATH}")
	endif (X11_Xscreensaver_FOUND)
elseif (WIN32)
	set(SOURCES ${SOURCES} "idle_win.cpp")
endif (APPLE)

add_definitions(-DUNICODE)

qt5_wrap_cpp(MOC_SOURCES ${HEADERS})

add_library(idle STATIC ${SOURCES} ${MOC_SOURCES})

if (UNIX AND NOT APPLE)
    target_link_libraries(idle Qt5::Widgets Qt5::X11Extras)
else (UNIX AND NOT APPLE)
    target_link_libraries(idle Qt5::Widgets)
endif (UNIX AND NOT APPLE)

set_target_properties(idle PROPERTIES ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/src/libs"
	COMPILE_FLAGS "${CMAKE_SHARED_LIBRARY_C_FLAGS}"
	LINK_FLAGS "${CMAKE_SHARED_LIBRARY_C_FLAGS}")
