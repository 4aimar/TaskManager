cmake_minimum_required (VERSION 2.8.1)
cmake_policy(SET CMP0042 NEW)

set(PROJECT_NAME "TaskManager")
project (${PROJECT_NAME} CXX)

# Global variables
set(MODULE_DIR cmake)
set(SOURCE_DIR src)
set(INCLUDE_DIR include)
set(LIB_DIR lib)
set(LIB_INCLUDE_DIR "${INCLUDE_DIR}/${PROJECT_NAME}")
set(TEST_DIR test)
set(IMPLEMENTATION_DIR detail)
set(TESTER_NAME "${PROJECT_NAME}-tester")
set(LIB_NAME ${PROJECT_NAME})

set(CLANG_TIDY_CONFIG_SOURCE "${PROJECT_SOURCE_DIR}/.clang-tidy")

# CMake properties
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set(CMAKE_INCLUDE_DIRECTORIES_PROJECT_BEFORE ON)
set(PREDEFINED_TARGETS_FOLDER "CMake")
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/${MODULE_DIR})
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_BUILD_TYPE ${BUILD_TYPE})

message(STATUS "Building ${BUILD_TYPE} ${PROJECT_NAME}\n----------------------------")

# Check compiler version
include(CheckCompiler)
check_compiler()

add_subdirectory(lib)

# Set compiler flags
include(CompilerFlags)

# Source files.
include(Sources)

include_directories (${PROJECT_SOURCE_DIR})
include_directories (${PROJECT_SOURCE_DIR}/${INCLUDE_DIR})

# Add required unix libraries
add_library(${LIB_NAME} STATIC ${LIB_SOURCE_FILES})

if(TASK_MANAGER_BUILD_TESTS)
  include(EnableTests)
endif()

if(TASK_MANAGER_ENABLE_CLANGTIDY AND CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  include(EnableClangTidy)
  enable_clangtidy(SOURCES "${PROJECT_SOURCE_DIR}/${SOURCE_DIR}")
endif()

if(TASK_MANAGER_ENABLE_COVERAGE)
  include(EnableTestCoverage)
  add_coverage_flags(TARGETS ${BIN_NAME} ${LIB_NAME})
endif()


install(TARGETS ${LIB_NAME} DESTINATION ${LIB_DIR})
install(DIRECTORY ${LIB_INCLUDE_DIR} DESTINATION ${INCLUDE_DIR})

set(TASK_MANAGER_INCLUDE_DIR ${CMAKE_INSTALL_PREFIX}/${INCLUDE_DIR})
set(TASK_MANAGER_INCLUDE_DIRS ${TASK_MANAGER_INCLUDE_DIR} )
set(TASK_MANAGER_LIBRARY_DIRS ${CMAKE_INSTALL_PREFIX}/${LIB_INSTALL_DIR})
set(TASK_MANAGER_LIBRARY ${PROJECT_NAME})
