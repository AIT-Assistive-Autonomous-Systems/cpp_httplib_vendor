cmake_minimum_required(VERSION 3.10)
project(cpp_httplib_vendor CXX)

find_package(ament_cmake REQUIRED)
include(ExternalProject)

ExternalProject_Add(cpp_httplib_src
  GIT_REPOSITORY https://github.com/yhirose/cpp-httplib.git
  GIT_TAG v0.20.1
  CONFIGURE_COMMAND ""
  BUILD_COMMAND ""
  INSTALL_COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_CURRENT_BINARY_DIR}/include/cpp-httplib
    && ${CMAKE_COMMAND} -E copy
         <SOURCE_DIR>/httplib.h
         ${CMAKE_CURRENT_BINARY_DIR}/include/cpp-httplib/httplib.h
  PATCH_COMMAND
    bash -c "cd <SOURCE_DIR> && patch --batch -p1 < ${CMAKE_CURRENT_SOURCE_DIR}/patches/namespace_exposed.patch"
  UPDATE_DISCONNECTED TRUE
)

add_library(cpp_httplib_vendor INTERFACE)
add_dependencies(cpp_httplib_vendor cpp_httplib_src)
target_include_directories(cpp_httplib_vendor INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

install(
  DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/include/cpp-httplib
  DESTINATION include
)

ament_export_include_directories(include)
ament_package()
