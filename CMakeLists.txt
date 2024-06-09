# CMake file for compiling the sqlite3 static library under Windows (for ease of use)
#
# Copyright (c) 2012-2023 Sebastien Rombauts (sebastien.rombauts@gmail.com)
#
# Distributed under the MIT License (MIT) (See accompanying file LICENSE.txt
# or copy at http://opensource.org/licenses/MIT) 

# add sources of the "sqlite3" static library
add_library(sqlite3
 sqlite3.c
 sqlite3.h
)

if (WIN32)
    if (BUILD_SHARED_LIBS)
            add_definitions("-DSQLITE_API=__declspec(dllexport)")
    endif()
endif()

target_include_directories(sqlite3
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

if (UNIX AND CMAKE_COMPILER_IS_GNUCXX)
    if (CMAKE_CXX_COMPILER_VERSION VERSION_GREATER 7.0)
        target_compile_options(sqlite3 PRIVATE "-Wimplicit-fallthrough=0")
    endif()
    if (CMAKE_CXX_COMPILER_VERSION VERSION_GREATER 8.0)
        target_compile_options(sqlite3 PRIVATE "-Wno-cast-function-type")
    endif()
endif()

# Allow the library to be installed via "make install" and found with "find_package"

include(GNUInstallDirs)
install(TARGETS sqlite3
    EXPORT ${PROJECT_NAME}Targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    COMPONENT libraries)
install(FILES sqlite3.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} COMPONENT headers)
