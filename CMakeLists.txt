cmake_minimum_required(VERSION 3.0)

project(sdptransform VERSION 1.2.10)

# For CMake >= 3.1.
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(test)
add_subdirectory(readme-helper)

set(SOURCE_FILES
	src/grammar.cpp
	src/parser.cpp
	src/writer.cpp
)

set(HEADER_FILES
	include/sdptransform.hpp
	include/json.hpp
)

add_library(sdptransform STATIC ${SOURCE_FILES} ${HEADER_FILES})

target_include_directories(sdptransform PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

install(TARGETS sdptransform DESTINATION lib)
install(FILES ${HEADER_FILES} DESTINATION include/sdptransform)
