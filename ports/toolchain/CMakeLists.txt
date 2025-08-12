cmake_minimum_required(VERSION 3.31)
project(toolchain LANGUAGES C CXX ASM_MASM)

# Macros
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

# Ninja
if(NOT CMAKE_GENERATOR STREQUAL "Ninja")
  message(FATAL_ERROR "Generator is not Ninja: ${CMAKE_GENERATOR}")
endif()

# Programs
foreach(PROGRAM IN ITEMS MAKE_PROGRAM C_COMPILER CXX_COMPILER ASM_MASM_COMPILER RC_COMPILER LINKER AR MT)
  cmake_path(CONVERT "${CMAKE_${PROGRAM}}" TO_CMAKE_PATH_LIST CMAKE_${PROGRAM} NORMALIZE)
  get_filename_component(CMAKE_${PROGRAM} "${CMAKE_${PROGRAM}}" REALPATH)
  get_filename_component(CMAKE_${PROGRAM} "${CMAKE_${PROGRAM}}" ABSOLUTE)
  if(NOT EXISTS "${CMAKE_${PROGRAM}}")
    message(FATAL_ERROR "Normalized CMAKE_${PROGRAM} missing: ${CMAKE_${PROGRAM}}")
  endif()
endforeach()

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

# CPU
set(ARCH "${VCPKG_TARGET_ARCHITECTURE}")

# CRT
cmake_path(CONVERT "$ENV{VCToolsInstallDir}" TO_CMAKE_PATH_LIST CRT NORMALIZE)
cmake_path(CONVERT "${CRT}/include" TO_CMAKE_PATH_LIST CRT_INCLUDE NORMALIZE)
cmake_path(CONVERT "${CRT}/lib/${VCPKG_TARGET_ARCHITECTURE}" TO_CMAKE_PATH_LIST CRT_LIB NORMALIZE)

# SDK
cmake_path(CONVERT "$ENV{WindowsSdkDir}" TO_CMAKE_PATH_LIST SDK NORMALIZE)
cmake_path(CONVERT "$ENV{WindowsSDKVersion}" TO_CMAKE_PATH_LIST SDK_VERSION NORMALIZE)
cmake_path(CONVERT "${SDK}/include/${SDK_VERSION}/ucrt" TO_CMAKE_PATH_LIST SDK_INCLUDE_UCRT NORMALIZE)
cmake_path(CONVERT "${SDK}/include/${SDK_VERSION}/um" TO_CMAKE_PATH_LIST SDK_INCLUDE_UM NORMALIZE)
cmake_path(CONVERT "${SDK}/include/${SDK_VERSION}/shared" TO_CMAKE_PATH_LIST SDK_INCLUDE_SHARED NORMALIZE)
cmake_path(CONVERT "${SDK}/include/${SDK_VERSION}/winrt" TO_CMAKE_PATH_LIST SDK_INCLUDE_WINRT NORMALIZE)
cmake_path(CONVERT "${SDK}/include/${SDK_VERSION}/cppwinrt" TO_CMAKE_PATH_LIST SDK_INCLUDE_CPPWINRT NORMALIZE)
cmake_path(CONVERT "${SDK}/lib/${SDK_VERSION}/ucrt/${VCPKG_TARGET_ARCHITECTURE}" TO_CMAKE_PATH_LIST SDK_LIB_UCRT NORMALIZE)
cmake_path(CONVERT "${SDK}/lib/${SDK_VERSION}/um/${VCPKG_TARGET_ARCHITECTURE}" TO_CMAKE_PATH_LIST SDK_LIB_UM NORMALIZE)

configure_file(toolchain.cmake.in ${CMAKE_BINARY_DIR}/toolchain.cmake @ONLY LF)
install(FILES ${CMAKE_BINARY_DIR}/toolchain.cmake DESTINATION share)
