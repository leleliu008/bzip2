cmake_minimum_required(VERSION 3.0)

project(bzip2 C)

set(PACKAGE_NAME  ${PROJECT_NAME})
set(PACKAGE_VERSION 1.0.8)
set(PACKAGE_INSTALL_DIR ${CMAKE_INSTALL_PREFIX})

###############################################################################################

option(BUILD_SHARED_LIBS   "if build shared libraries" ON)
option(BUILD_STATIC_LIBS   "if build static libraries" ON)
option(INSTALL_LIBRARIES   "if install libraries"   ON)
option(INSTALL_EXECUTABLES "if install executables" ON)

###############################################################################################

if (NOT BUILD_SHARED_LIBS AND NOT BUILD_STATIC_LIBS)
    message(FATAL_ERROR "at least one of BUILD_STATIC_LIBS and BUILD_SHARED_LIBS should be enabled.")
endif()

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

configure_file(libbz2.pc.in libbz2.pc @ONLY)

add_definitions(-D_FILE_OFFSET_BITS=64)

###############################################################################################

add_executable(bzip2 bzip2.c)
add_executable(bzip2recover bzip2recover.c)

###############################################################################################

set(SRC_LIBBZ2 blocksort.c huffman.c crctable.c randtable.c compress.c decompress.c bzlib.c)

if (BUILD_SHARED_LIBS)
    add_library(bz2-shared SHARED ${SRC_LIBBZ2})
    set_target_properties(bz2-shared PROPERTIES OUTPUT_NAME bz2 CLEAN_DIRECT_OUTPUT 1 EXPORT_NAME shared)

    target_link_libraries(bzip2 bz2-shared)
endif()

if (BUILD_STATIC_LIBS)
    add_library(bz2-static STATIC ${SRC_LIBBZ2})
    set_target_properties(bz2-static PROPERTIES OUTPUT_NAME bz2 CLEAN_DIRECT_OUTPUT 1 EXPORT_NAME static)

    target_link_libraries(bzip2 bz2-static)
endif()

###############################################################################################

include(GNUInstallDirs)

if (INSTALL_LIBRARIES)
    if (BUILD_STATIC_LIBS)
        install(TARGETS bz2-static LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
    endif()

    if (BUILD_SHARED_LIBS)
        install(TARGETS bz2-shared LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
    endif()

    install(FILES ${CMAKE_BINARY_DIR}/libbz2.pc   DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
    install(FILES ${CMAKE_SOURCE_DIR}/bzlib.h     DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
endif()

if (INSTALL_EXECUTABLES)
    set(BIN_PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)

    install(TARGETS bzip2        RUNTIME         DESTINATION ${CMAKE_INSTALL_BINDIR})
    install(TARGETS bzip2recover RUNTIME         DESTINATION ${CMAKE_INSTALL_BINDIR})
    install(FILES ${CMAKE_SOURCE_DIR}/bzgrep     DESTINATION ${CMAKE_INSTALL_BINDIR} PERMISSIONS ${BIN_PERMISSIONS})
    install(FILES ${CMAKE_SOURCE_DIR}/bzmore     DESTINATION ${CMAKE_INSTALL_BINDIR} PERMISSIONS ${BIN_PERMISSIONS})
    install(FILES ${CMAKE_SOURCE_DIR}/bzdiff     DESTINATION ${CMAKE_INSTALL_BINDIR} PERMISSIONS ${BIN_PERMISSIONS})

    install(FILES ${CMAKE_SOURCE_DIR}/bzip2.1    DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
    install(FILES ${CMAKE_SOURCE_DIR}/bzgrep.1   DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
    install(FILES ${CMAKE_SOURCE_DIR}/bzmore.1   DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
    install(FILES ${CMAKE_SOURCE_DIR}/bzdiff.1   DESTINATION ${CMAKE_INSTALL_MANDIR}/man1)
endif()
