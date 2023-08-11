cmake_minimum_required(VERSION 3.0)

# option() honors normal variables.
# see: https://cmake.org/cmake/help/git-stage/policy/CMP0077.html
if(POLICY CMP0077)
  cmake_policy(SET CMP0077 NEW)
endif()

project(OpenBFS2 VERSION 0.0.1 LANGUAGES C CXX)

set(CMAKE_C_STANDARD 99)

# Default Build Type to be Release
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release" CACHE STRING
      "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel."
      FORCE)
endif(NOT CMAKE_BUILD_TYPE)

include(CheckIncludeFile)
include(CheckSymbolExists)
include(GNUInstallDirs)

macro(setup_include_and_definitions TARGET_NAME)
  target_include_directories(${TARGET_NAME}
    PUBLIC  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
  )
endmacro()

macro(add_openbfs2_headers_and_sources HDRS_LIST_NAME SRCS_LIST_NAME)
  list(APPEND ${HDRS_LIST_NAME} ${PROJECT_SOURCE_DIR}/include/openbfs2_macros.h)
  file(GLOB IMPL_SOURCES CONFIGURE_DEPENDS "${PROJECT_SOURCE_DIR}/src/impl_*.c")
  list(APPEND ${SRCS_LIST_NAME} ${IMPL_SOURCES})
endmacro()

#
# library : openbfs2
#

set (OPENBFS2_HDRS)
set (OPENBFS2_SRCS)
add_openbfs2_headers_and_sources(OPENBFS2_HDRS OPENBFS2_SRCS)

add_library(openbfs2 ${OPENBFS2_HDRS} ${OPENBFS2_SRCS})
set_target_properties(openbfs2 PROPERTIES PUBLIC_HEADER "${OPENBFS2_HDRS}")
setup_include_and_definitions(openbfs2)
target_include_directories(openbfs2
  PUBLIC $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/openbfs2>
)
add_library(OpenBFS2::openbfs2 ALIAS openbfs2)

#
# program : openbfs2_demo
#

add_executable(openbfs2_demo ${PROJECT_SOURCE_DIR}/src/utils/main.c)
target_link_libraries(openbfs2_demo PRIVATE openbfs2)
add_executable(OpenBFS2::openbfs2_demo ALIAS openbfs2_demo)

