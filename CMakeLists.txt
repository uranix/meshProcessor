cmake_minimum_required(VERSION 2.8)

project(mesh3d)

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

set (mesh3d_SOURCES	vol_mesh.cpp mesh.cpp common.cpp vtk_stream.cpp)

set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")

if(NOT DEFINED USE_METIS OR USE_METIS)
	find_package(METIS 5)
	set(USE_METIS ${METIS_FOUND})
endif()

if(USE_METIS)
	set (mesh3d_SOURCES "${mesh3d_SOURCES}" graph.cpp mesh_graph.cpp)
endif()

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.in ${CMAKE_CURRENT_SOURCE_DIR}/config.h @ONLY)

add_library(mesh3d STATIC ${mesh3d_SOURCES})

find_package(Doxygen)
if(DOXYGEN_FOUND)
	configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile @ONLY)
	add_custom_target(
		doc ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
		COMMENT "Generating API documentation with Doxygen" VERBATIM
	)
endif(DOXYGEN_FOUND) 

enable_testing()
include_directories(${CMAKE_CURRENT_SOURCE_DIR})
add_subdirectory(test)
