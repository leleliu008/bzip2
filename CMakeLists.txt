cmake_minimum_required(VERSION 3.0)

project(bzip2 C)

set(PACKAGE_NAME  ${PROJECT_NAME})
set(PACKAGE_VERSION 1.0.8)
set(PACKAGE_INSTALL_DIR ${CMAKE_INSTALL_PREFIX})

###############################################################################################

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

###############################################################################################

add_definitions(-D_FILE_OFFSET_BITS=64)

###############################################################################################

set(SRC_LIBBZ2 blocksort.c huffman.c crctable.c randtable.c compress.c decompress.c bzlib.c)

add_library(bz2-shared SHARED ${SRC_LIBBZ2})
set_target_properties(bz2-shared PROPERTIES OUTPUT_NAME bz2 CLEAN_DIRECT_OUTPUT 1 EXPORT_NAME shared)

add_library(bz2-static STATIC ${SRC_LIBBZ2})
set_target_properties(bz2-static PROPERTIES OUTPUT_NAME bz2 CLEAN_DIRECT_OUTPUT 1 EXPORT_NAME static)

###############################################################################################

add_executable(bzip2        bzip2.c)
add_executable(bzip2recover bzip2recover.c)

target_link_libraries(bzip2 bz2-static)

###############################################################################################

configure_file(libbz2.pc.in libbz2.pc @ONLY)

###############################################################################################

include(GNUInstallDirs)

install(PROGRAMS bzgrep bzmore bzdiff       DESTINATION ${CMAKE_INSTALL_BINDIR} COMPONENT bin)
install(TARGETS bzip2 bzip2recover  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} COMPONENT bin)
install(TARGETS bz2-shared          LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT lib)
install(TARGETS bz2-static          ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT lib)

install(FILES bzlib.h                       DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}       COMPONENT dev)
install(FILES ${CMAKE_BINARY_DIR}/libbz2.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig COMPONENT dev)

install(FILES bzgrep.1 bzmore.1 bzdiff.1 bzip2.1 DESTINATION ${CMAKE_INSTALL_MANDIR}/man1 COMPONENT man)
