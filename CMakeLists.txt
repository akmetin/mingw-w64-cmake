cmake_minimum_required(VERSION 2.8.11)
include(ExternalProject)

if(NOT ${CMAKE_GENERATOR} MATCHES "Ninja")
    message(WARNING "Generator “${CMAKE_GENERATOR}” is unsupported!\nTry Ninja if you encounter problems.")
endif()

set(TARGET_ARCH "x86_64-w64-mingw32" CACHE STRING "The target architecture for this toolchain.")
string(REGEX MATCH "^[^-]+" TARGET_CPU ${TARGET_ARCH})
set(MAKEJOBS "1" CACHE STRING "The number of parallel make jobs to use for each package.")

set(MINGW_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}/mingw)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/toolchain.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/toolchain.cmake)
set(TOOLCHAIN_FILE ${CMAKE_CURRENT_BINARY_DIR}/toolchain.cmake)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/exec.in ${CMAKE_CURRENT_BINARY_DIR}/exec)
set(EXEC ${CMAKE_CURRENT_BINARY_DIR}/exec)
set(MAKE ${EXEC} make -j${MAKEJOBS})

execute_process(COMMAND date --rfc-3339=date
    OUTPUT_VARIABLE RFCDATE OUTPUT_STRIP_TRAILING_WHITESPACE
)
string(REGEX REPLACE "-" "" BUILDDATE ${RFCDATE})

add_subdirectory(toolchain)
add_subdirectory(packages)