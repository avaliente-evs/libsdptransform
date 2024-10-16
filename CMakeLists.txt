cmake_minimum_required(VERSION 3.0)

project(sdptransform VERSION 1.2.11)

# For CMake >= 3.1.
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

option(SDPTRANSFORM_FETCH_NLOHMANN_JSON "Use cmake FetchContent to get nlohmann json" ON)
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
set(NLOHMANN_JSON_VERSION 3.11.3 CACHE STRING "nlohmann library version to use")

if (SDPTRANSFORM_FETCH_NLOHMANN_JSON)
  include(FetchContent)

  FetchContent_Declare(NLOHMANN_JSON
    URL https://github.com/nlohmann/json/releases/download/v${NLOHMANN_JSON_VERSION}/json.tar.xz)
  FetchContent_MakeAvailable(NLOHMANN_JSON)
else()
  find_package(nlohmann_json ${NLOHMANN_JSON_VERSION} REQUIRED)
endif()

add_subdirectory(test)
add_subdirectory(readme-helper)

set(SOURCE_FILES
	src/grammar.cpp
	src/parser.cpp
	src/writer.cpp
)

set(HEADER_FILES
	include/sdptransform.hpp
)

add_library(sdptransform ${SOURCE_FILES} ${HEADER_FILES})

target_include_directories(sdptransform PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

target_link_libraries(sdptransform PUBLIC nlohmann_json::nlohmann_json)

install(TARGETS sdptransform DESTINATION lib)
install(FILES ${HEADER_FILES} DESTINATION include/sdptransform)
