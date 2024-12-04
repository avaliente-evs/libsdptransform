cmake_minimum_required(VERSION 3.19)

# Read version from vcpkg.json
file(READ ${CMAKE_CURRENT_SOURCE_DIR}/vcpkg.json VCPKG_JSON)
string(JSON MANIFEST_PROJECT_NAME GET ${VCPKG_JSON} "name")
string(JSON MANIFEST_PROJECT_VERSION GET ${VCPKG_JSON} "version")
string(JSON MANIFEST_PROJECT_DESCRIPTION GET ${VCPKG_JSON} "description")

project(${MANIFEST_PROJECT_NAME}
        VERSION ${MANIFEST_PROJECT_VERSION}
        DESCRIPTION ${MANIFEST_PROJECT_DESCRIPTION})

option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
option(SDPTRANSFORM_BUILD_TESTS "Build tests" OFF)
option(SDPTRANSFORM_BUILD_README_HELPER "Build readme helper" OFF)

find_package(nlohmann_json CONFIG REQUIRED)

if (SDPTRANSFORM_BUILD_TESTS)
  add_subdirectory(test)
endif()
if (SDPTRANSFORM_BUILD_README_HELPER)
  add_subdirectory(readme-helper)
endif()

set(SOURCE_FILES
	src/grammar.cpp
	src/parser.cpp
	src/writer.cpp
)

set(HEADER_FILES
	include/sdptransform.hpp
)

add_library(${CMAKE_PROJECT_NAME} ${SOURCE_FILES} ${HEADER_FILES})

target_include_directories(${CMAKE_PROJECT_NAME} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

target_link_libraries(${CMAKE_PROJECT_NAME} PUBLIC nlohmann_json::nlohmann_json)

# Install project to make it available using find_package
install(TARGETS ${CMAKE_PROJECT_NAME}
        EXPORT ${CMAKE_PROJECT_NAME}-targets)
install(EXPORT ${CMAKE_PROJECT_NAME}-targets
        FILE ${CMAKE_PROJECT_NAME}-targets.cmake
        DESTINATION cmake)
install(FILES ${HEADER_FILES}
        DESTINATION include/${CMAKE_PROJECT_NAME})
include(CMakePackageConfigHelpers)
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.cmake.in
                              ${CMAKE_CURRENT_BINARY_DIR}/cmake/${CMAKE_PROJECT_NAME}-config.cmake
                              INSTALL_DESTINATION cmake)
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/cmake/${CMAKE_PROJECT_NAME}-version.cmake
                                 VERSION ${PROJECT_VERSION}
                                 COMPATIBILITY SameMajorVersion)
install(FILES
          ${CMAKE_CURRENT_BINARY_DIR}/cmake/${CMAKE_PROJECT_NAME}-config.cmake
          ${CMAKE_CURRENT_BINARY_DIR}/cmake/${CMAKE_PROJECT_NAME}-version.cmake
        DESTINATION
          cmake)
export(EXPORT ${CMAKE_PROJECT_NAME}-targets
       FILE ${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}-targets.cmake)
