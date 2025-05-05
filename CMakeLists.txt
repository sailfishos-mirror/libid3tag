cmake_minimum_required(VERSION 3.10)
project(id3tag VERSION 0.16.3)

option(BUILD_SHARED_LIBS "Build dynamic library" ON)

# The new SOVERSION. This is 0.16.2 by default
# The general policy is that minor versions of the library (e.g., 0.16.1,
# 0.16.2) don't constitute an ABI breakage. Major versions (e.g., 0.17, 0.18)
# do constitute an ABI breakage.
set(LIBRARY_SOVERSION 0)

include(GNUInstallDirs)

# Build (see src/CMakeLists.txt)
add_library(id3tag
    src/compat.c
    src/crc.c
    src/debug.c
    src/field.c
    src/file.c
    src/frame.c
    src/frametype.c
    src/genre.c
    src/latin1.c
    src/parse.c
    src/render.c
    src/tag.c
    src/ucs4.c
    src/utf16.c
    src/utf8.c
    src/util.c
    src/version.c
)

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/include/id3tag.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/id3tag.h
  @ONLY
)

target_include_directories(id3tag PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
if(WIN32 AND BUILD_SHARED_LIBS)
    set_target_properties(id3tag PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

set_target_properties(id3tag PROPERTIES
  VERSION ${CMAKE_PROJECT_VERSION}
  SOVERSION ${LIBRARY_SOVERSION}
)

include(CheckIncludeFile)
check_include_file(sys/stat.h HAVE_SYS_STAT_H)
if(HAVE_SYS_STAT_H)
  target_compile_definitions(id3tag PRIVATE HAVE_SYS_STAT_H)
endif()

check_include_file(unistd.h HAVE_UNISTD_H)
if(HAVE_UNISTD_H)
  target_compile_definitions(id3tag PRIVATE HAVE_UNISTD_H)
endif()

check_include_file(assert.h HAVE_ASSERT_H)
if(HAVE_ASSERT_H)
  target_compile_definitions(id3tag PRIVATE HAVE_ASSERT_H)
endif()

include(CheckFunctionExists)
check_function_exists(ftruncate HAVE_FTRUNCATE)
if(HAVE_FTRUNCATE)
  target_compile_definitions(id3tag PRIVATE HAVE_FTRUNCATE)
endif()

find_package(ZLIB REQUIRED)
target_link_libraries(id3tag PUBLIC ZLIB::ZLIB)

#
# Installation
#

include(CMakePackageConfigHelpers)

# Library files
install(TARGETS id3tag
  EXPORT id3tagTargets
  ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
  LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
  RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
  INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

# Header files
install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/id3tag.h"
  DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
)

# pkgconfig
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/packaging/id3tag.pc.in
  ${CMAKE_CURRENT_BINARY_DIR}/packaging/id3tag.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/packaging/id3tag.pc DESTINATION "${CMAKE_INSTALL_LIBDIR}/pkgconfig")

# CMake config
set(ID3TAG_INSTALL_CMAKEDIR "${CMAKE_INSTALL_LIBDIR}/cmake/id3tag")
install(
  EXPORT id3tagTargets
  FILE id3tagTargets.cmake
  NAMESPACE id3tag::
  DESTINATION "${ID3TAG_INSTALL_CMAKEDIR}"
)
configure_package_config_file(packaging/id3tagConfig.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/packaging/id3tagConfig.cmake"
  INSTALL_DESTINATION "${ID3TAG_INSTALL_CMAKEDIR}"
)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/packaging/id3tagConfigVersion.cmake"
  VERSION "${CMAKE_PROJECT_VERSION}"
  COMPATIBILITY SameMajorVersion
)
install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/packaging/id3tagConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/packaging/id3tagConfigVersion.cmake"
  DESTINATION "${ID3TAG_INSTALL_CMAKEDIR}"
)
