cmake_minimum_required(VERSION 3.23)

project(imgui VERSION 1.91.8 LANGUAGES CXX)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

add_library(imgui STATIC)
add_library(imgui::imgui ALIAS imgui)

target_include_directories(imgui PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_compile_options(imgui PUBLIC
  -Wall -Wextra -Wpedantic
)

target_compile_features(imgui PUBLIC c_std_17)

target_sources(imgui
  PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui_draw.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui_tables.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui_widgets.cpp
  PUBLIC
    FILE_SET HEADERS
    BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}
    FILES
      ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui.h
)

# --------------- Export for the build tree  ----------------

export(
  TARGETS ${PROJECT_NAME}
  NAMESPACE ${PROJECT_NAME}::
  FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Targets.cmake"
)

# -------------------- Install & package --------------------

install(TARGETS imgui
  EXPORT imguiTargets
  FILE_SET HEADERS
)

write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/imguiConfigVersion.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
  "${CMAKE_CURRENT_SOURCE_DIR}/cmake/imguiConfig.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/imguiConfig.cmake"
  INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/imgui"
)

install(EXPORT imguiTargets
  NAMESPACE imgui::
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/imgui"
)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/imguiConfig.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/imguiConfigVersion.cmake"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/imgui"
)
