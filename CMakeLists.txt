cmake_minimum_required(VERSION 2.6)

project(mym)
set(SOVERSION 0)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_C_FLAGS)
  #release comes with -O3 by default
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_C_FLAGS)

enable_language(C)

######################################################################
# compiler tests
# these need ot be done early (before further tests).
#####################################################################

include(CheckCCompilerFlag)

########################################################################
# User input options                                                   #
########################################################################
option(BUILD_SHARED_LIBS "Build shared libs" ON)
if (NOT DEFINED LIB)
  set(LIB "lib")
endif(NOT DEFINED LIB)

option(ENABLE_TESTING "Enable testing" OFF)
if(ENABLE_TESTING)
  enable_testing()
endif(ENABLE_TESTING)

option(CMAKE_VERBOSE_MAKEFILE "Verbose makefile" OFF)

option(ENABLE_COVERAGE_BUILD "Do a coverage build" OFF)
if(ENABLE_COVERAGE_BUILD)
  message(STATUS "Enabling coverage build")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} --coverage -O0")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} --coverage -O0")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} --coverage")
  set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} --coverage")
endif()

########################################################################
# Basic system tests (standard libraries, headers, functions, types)   #
########################################################################
include(CheckIncludeFile)
foreach(HEADER math.h)
  check_include_file(${HEADER} FOUND_${HEADER})
  if(NOT FOUND_${HEADER})
    message(FATAL_ERROR "Could not find needed header - ${HEADER}")
  endif(NOT FOUND_${HEADER})
endforeach(HEADER)

set(MATH_LIBRARIES "m" CACHE STRING "math library")
mark_as_advanced( MATH_LIBRARIES )
include(CheckLibraryExists)
foreach(FUNC sin cos)
  check_library_exists(${MATH_LIBRARIES} ${FUNC} "" FOUND_${FUNC}_${MATH_LIBRARIES})
  if(NOT FOUND_${FUNC}_${MATH_LIBRARIES})
    message(FATAL_ERROR "Could not find needed math function - ${FUNC}")
  endif(NOT FOUND_${FUNC}_${MATH_LIBRARIES})
endforeach(FUNC)

######################################
# Include the following subdirectory # 
######################################
add_subdirectory(src)
add_subdirectory(tests)
