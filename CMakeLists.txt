##############################################################################
# Copyright (c) 2018, Lawrence Livermore National Security, LLC.
# Produced at the Lawrence Livermore National Laboratory
#
# Created by David Beckingsale, david@llnl.gov
# LLNL-CODE-747640
#
# All rights reserved.
#
# This file is part of Umpire.
#
# For details, see https://github.com/LLNL/Umpire
# Please also see the LICENSE file for MIT license.
##############################################################################
cmake_minimum_required(VERSION 3.9)

cmake_policy(SET CMP0048 NEW)
cmake_policy(SET CMP0057 NEW)

project(Umpire 
  LANGUAGES CXX C
  VERSION 0.2.0)

set(ENABLE_OPENMP On CACHE Bool "")
set(ENABLE_CUDA On CACHE Bool "")
set(ENABLE_COPY_HEADERS On CACHE Bool "")
set(ENABLE_TESTS On CACHE Bool "")
set(ENABLE_BENCHMARKS On CACHE Bool "")
set(ENABLE_GMOCK On CACHE Bool "")
option(ENABLE_EXAMPLES "Build Umpire examples" On)
option(ENABLE_LOGGING "Build Umpire with Logging enabled" On)
option(ENABLE_SLIC "Build Umpire with SLIC logging" Off)
option(ENABLE_ASSERTS "Build Umpire with assert() enabled" On)
set(ENABLE_GTEST_DEATH_TESTS ${ENABLE_ASSERTS} CACHE Bool "")

################################
# BLT
################################
if (NOT EXISTS ${PROJECT_SOURCE_DIR}/blt/SetupBLT.cmake)
  message(FATAL_ERROR "\
  The BLT submodule is not present. \
  If in git repository run the following two commands:\n \
  git submodule init\n \
  git submodule update")
endif()

include(blt/SetupBLT.cmake)

include(cmake/SetupUmpireThirdParty.cmake)

configure_file(
  umpire-config.cmake.in
  "${PROJECT_BINARY_DIR}/umpire-config.cmake" @ONLY)

install(FILES
  "${PROJECT_BINARY_DIR}/umpire-config.cmake"
  DESTINATION share/umpire/cmake)

install(EXPORT umpire-targets DESTINATION share/umpire/cmake)

add_subdirectory(src)

if (ENABLE_TESTS)
  set (UMPIRE_TEST_OUTPUT_DIR ${CMAKE_BINARY_DIR}/test)
  add_subdirectory(tests)
endif ()
if (ENABLE_BENCHMARKS)
  set (UMPIRE_BENCHMARK_OUTPUT_DIR ${CMAKE_BINARY_DIR}/benchmark)
  add_subdirectory(benchmarks)
endif ()
if (ENABLE_EXAMPLES)
add_subdirectory(examples)
endif ()

add_subdirectory(docs)
