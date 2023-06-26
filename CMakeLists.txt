cmake_minimum_required(VERSION 3.0 FATAL_ERROR)

option(JDKSMIDI_BUILD_UTILITIES "Enables building of utility applications." OFF)
option(JDKSMIDI_BUILD_TESTS "Enables building of test applications." OFF)

set(_JDKSMIDI_INCLUDE_DIRECTORY_NAME "include")
set(_JDKSMIDI_INCLUDE_DIRECTORY_PATH "include/jdksmidi")
set(_JDKSMIDI_SOURCE_DIRECTORY_NAME "src")
set(_JDKSMIDI_SOURCE_DIRECTORY_PATH "${_JDKSMIDI_SOURCE_DIRECTORY_NAME}")
set(_JDKSMIDI_VERSION_FILE_PATH "${_JDKSMIDI_INCLUDE_DIRECTORY_PATH}/version.h")
set(_JDKSMIDI_LIBRARY_NAME "jdksmidi")

project(JDKSMIDI VERSION 2014.08.11 LANGUAGES CXX)

set(HEADER_FILES
	advancedsequencer.h
	driver.h
	driverdump.h
	edittrack.h
	file.h
	fileread.h
	filereadmultitrack.h
	fileshow.h
	filewrite.h
	filewritemultitrack.h
	keysig.h
	manager.h
	matrix.h
	midi.h
	msg.h
	multitrack.h
	parser.h
	process.h
	queue.h
	sequencer.h
	showcontrol.h
	showcontrolhandler.h
	smpte.h
	song.h
	sysex.h
	tempo.h
	tick.h
	track.h
	utils.h
	version.h
	world.h
)

set(SOURCE_FILES
	jdksmidi_advancedsequencer.cpp
	jdksmidi_driver.cpp
	jdksmidi_driverdump.cpp
	jdksmidi_edittrack.cpp
	jdksmidi_file.cpp
	jdksmidi_fileread.cpp
	jdksmidi_filereadmultitrack.cpp
	jdksmidi_fileshow.cpp
	jdksmidi_filewrite.cpp
	jdksmidi_filewritemultitrack.cpp
	jdksmidi_keysig.cpp
	jdksmidi_manager.cpp
	jdksmidi_matrix.cpp
	jdksmidi_midi.cpp
	jdksmidi_msg.cpp
	jdksmidi_multitrack.cpp
	jdksmidi_parser.cpp
	jdksmidi_process.cpp
	jdksmidi_queue.cpp
	jdksmidi_sequencer.cpp
	jdksmidi_showcontrol.cpp
	jdksmidi_showcontrolhandler.cpp
	jdksmidi_smpte.cpp
	jdksmidi_sysex.cpp
	jdksmidi_tempo.cpp
	jdksmidi_tick.cpp
	jdksmidi_track.cpp
	jdksmidi_utils.cpp
)

if(WIN32)
	list(APPEND HEADER_FILES driverwin32.h)
	list(APPEND SOURCE_FILES win32/jdksmidi_driverwin32.cpp)
else()
	list(APPEND HEADER_FILES AlsaDriver.h)
	list(APPEND SOURCE_FILES alsa/AlsaDriver.cpp)
endif()

list(TRANSFORM HEADER_FILES PREPEND "${_JDKSMIDI_INCLUDE_DIRECTORY_PATH}/")
list(TRANSFORM SOURCE_FILES PREPEND "${_JDKSMIDI_SOURCE_DIRECTORY_PATH}/")

source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/${_JDKSMIDI_INCLUDE_DIRECTORY_PATH} PREFIX "Headers" FILES ${HEADER_FILES})
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/${_JDKSMIDI_SOURCE_DIRECTORY_PATH} PREFIX "Source" FILES ${SOURCE_FILES})

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/${_JDKSMIDI_VERSION_FILE_PATH}.in" "${CMAKE_CURRENT_SOURCE_DIR}/${_JDKSMIDI_VERSION_FILE_PATH}" @ONLY)

add_library(${_JDKSMIDI_LIBRARY_NAME} ${HEADER_FILES} ${SOURCE_FILES})

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/${_JDKSMIDI_INCLUDE_DIRECTORY_NAME})

if(JDKSMIDI_BUILD_UTILITIES OR JDKSMIDI_BUILD_TESTS)
	link_directories(${JDKSMIDI_BINARY_DIR})
endif()

if(JDKSMIDI_BUILD_UTILITIES)
	add_executable(create_midifile examples/create_midifile.cpp)
	target_link_libraries(create_midifile ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(jdksmidi_rewrite_midifile examples/jdksmidi_rewrite_midifile.cpp)
	target_link_libraries(jdksmidi_rewrite_midifile ${_JDKSMIDI_LIBRARY_NAME})
endif()

if(JDKSMIDI_BUILD_TESTS)
	add_executable(jdksmidi_test_drv examples/jdksmidi_test_drv.cpp)
	target_link_libraries(jdksmidi_test_drv ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(jdksmidi_test_multitrack examples/jdksmidi_test_multitrack.cpp)
	target_link_libraries(jdksmidi_test_multitrack ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(jdksmidi_test_multitrack1 examples/jdksmidi_test_multitrack1.cpp)
	target_link_libraries(jdksmidi_test_multitrack1 ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(jdksmidi_test_parse examples/jdksmidi_test_parse.cpp)
	target_link_libraries(jdksmidi_test_parse ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(jdksmidi_test_sequencer examples/jdksmidi_test_sequencer.cpp)
	target_link_libraries(jdksmidi_test_sequencer ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(jdksmidi_test_show examples/jdksmidi_test_show.cpp)
	target_link_libraries(jdksmidi_test_show ${_JDKSMIDI_LIBRARY_NAME})
endif()

if(JDKSMIDI_BUILD_UTILITIES)
	add_executable(rewrite_midifile examples/rewrite_midifile.cpp)
	target_link_libraries(rewrite_midifile ${_JDKSMIDI_LIBRARY_NAME})

	add_executable(vrm_music_gen examples/vrm_music_gen.cpp)
	target_link_libraries(vrm_music_gen ${_JDKSMIDI_LIBRARY_NAME})
endif()

include(GNUInstallDirs)

set(CONFIG_INSTALL_DIR "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(GENERATED_DIR "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(VERSION_CONFIG "${GENERATED_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
set(PROJECT_CONFIG "${GENERATED_DIR}/${PROJECT_NAME}Config.cmake")
set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(NAMESPACE "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
	"${VERSION_CONFIG}" COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
	"Config.cmake.in"
	"${PROJECT_CONFIG}"
	INSTALL_DESTINATION "${CONFIG_INSTALL_DIR}"
)

install(
	TARGETS ${_JDKSMIDI_LIBRARY_NAME}
	EXPORT "${TARGETS_EXPORT_NAME}"
	LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
	ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
	RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
	INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

install(
	FILES ${HEADER_FILES}
	DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}"
)

install(
	FILES "${PROJECT_CONFIG}" "${VERSION_CONFIG}"
	DESTINATION "${CONFIG_INSTALL_DIR}"
)

install(
	EXPORT "${TARGETS_EXPORT_NAME}"
	NAMESPACE "${NAMESPACE}"
	DESTINATION "${CONFIG_INSTALL_DIR}"
)
