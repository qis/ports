cmake_minimum_required(VERSION 3.31)
project(toolchain LANGUAGES C CXX ASM_MASM)

macro(config_flags flags)
  if(NOT DEFINED ${flags})
    set(${flags} "")
  endif()
  foreach(arg IN ITEMS ${ARGN})
    if(DEFINED ${arg})
      string(APPEND ${flags} " ${${arg}}")
    endif()
  endforeach()
  string(STRIP "${${flags}}" stripped_flags)
  separate_arguments(stripped_flags_list NATIVE_COMMAND "${stripped_flags}")
  set(filtered_flags "")
  foreach(flag IN LISTS stripped_flags_list)
    if(NOT "${flag}" MATCHES "^(/|-)[Nn][Oo][Ll][Oo][Gg][Oo]$")
      list(APPEND filtered_flags "${flag}")
    endif()
  endforeach()
  string(JOIN " " ${flags} ${filtered_flags})
endmacro()

macro(master_flags flags)
  config_flags(${ARGV})
  separate_arguments(filtered_flags NATIVE_COMMAND "${${flags}}")
  list(PREPEND filtered_flags "/nologo")
  string(JOIN " " ${flags} ${filtered_flags})
endmacro()

# Compiler Flags
master_flags(CMAKE_C_FLAGS_INIT VCPKG_C_FLAGS)
config_flags(CMAKE_C_FLAGS_DEBUG_INIT VCPKG_C_FLAGS_DEBUG)
config_flags(CMAKE_C_FLAGS_RELEASE_INIT VCPKG_C_FLAGS_RELEASE)

master_flags(CMAKE_CXX_FLAGS_INIT VCPKG_CXX_FLAGS)
config_flags(CMAKE_CXX_FLAGS_DEBUG_INIT VCPKG_CXX_FLAGS_DEBUG)
config_flags(CMAKE_CXX_FLAGS_RELEASE_INIT VCPKG_CXX_FLAGS_RELEASE)

master_flags(CMAKE_ASM_MASM_FLAGS_INIT)
config_flags(CMAKE_ASM_MASM_FLAGS_DEBUG_INIT)
config_flags(CMAKE_ASM_MASM_FLAGS_RELEASE_INIT)

master_flags(CMAKE_RC_FLAGS_INIT)

# Linker Flags
master_flags(CMAKE_EXE_LINKER_FLAGS_INIT)
config_flags(CMAKE_EXE_LINKER_FLAGS_DEBUG_INIT)
config_flags(CMAKE_EXE_LINKER_FLAGS_RELEASE_INIT VCPKG_LINKER_FLAGS VCPKG_LINKER_FLAGS_RELEASE)

master_flags(CMAKE_SHARED_LINKER_FLAGS_INIT)
config_flags(CMAKE_SHARED_LINKER_FLAGS_DEBUG_INIT)
config_flags(CMAKE_SHARED_LINKER_FLAGS_RELEASE_INIT VCPKG_LINKER_FLAGS VCPKG_LINKER_FLAGS_RELEASE)

master_flags(CMAKE_ARCHIVE_LINKER_FLAGS_INIT)
config_flags(CMAKE_ARCHIVE_LINKER_FLAGS_DEBUG_INIT)
config_flags(CMAKE_ARCHIVE_LINKER_FLAGS_RELEASE_INIT)

master_flags(CMAKE_STATIC_LINKER_FLAGS_INIT)
config_flags(CMAKE_STATIC_LINKER_FLAGS_DEBUG_INIT)
config_flags(CMAKE_STATIC_LINKER_FLAGS_RELEASE_INIT)

# Include Directories
cmake_path(CONVERT "$ENV{INCLUDE}" TO_CMAKE_PATH_LIST TOOLCHAIN_INCLUDE_DIRECTORIES NORMALIZE)

# Link Directories
cmake_path(CONVERT "$ENV{LIB}" TO_CMAKE_PATH_LIST TOOLCHAIN_LINK_DIRECTORIES NORMALIZE)

configure_file(toolchain.cmake.in ${CMAKE_BINARY_DIR}/toolchain.cmake @ONLY LF)
install(FILES ${CMAKE_BINARY_DIR}/toolchain.cmake DESTINATION share)
