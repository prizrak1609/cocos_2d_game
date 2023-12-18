cmake_minimum_required(VERSION 3.6)

set(lib_name chipmunk)
set(target_name ext_${lib_name})

include(../cmake/CocosExternalConfig.cmake)

set(BUILD_DEMOS OFF CACHE BOOL "" FORCE)
set(INSTALL_DEMOS OFF CACHE BOOL "" FORCE)
set(BUILD_SHARED OFF CACHE BOOL "" FORCE)
set(BUILD_STATIC ON CACHE BOOL "" FORCE)
set(INSTALL_STATIC ON CACHE BOOL "" FORCE)
set(LIB_INSTALL_DIR ${platform_spec_path} CACHE STRING "" FORCE)

include(FetchContent)

set(fetch_project ${target_name}_proj)

FetchContent_Declare(
	${fetch_project}
	GIT_REPOSITORY https://github.com/slembcke/Chipmunk2D.git
	GIT_TAG "Chipmunk-7.0.0"
)

FetchContent_MakeAvailable(${fetch_project})

FetchContent_GetProperties(${fetch_project})

if(NOT ${fetch_project}_POPULATED)
  FetchContent_Populate(${fetch_project})
  
  add_subdirectory(${${fetch_project}_SOURCE_DIR} ${${fetch_project}_BINARY_DIR})
endif()

add_library(${target_name} STATIC IMPORTED GLOBAL)

add_dependencies(${target_name} ${fetch_project})

set_target_properties(${target_name} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
)
if(WINDOWS)
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION_DEBUG "${platform_spec_path}/debug-lib/lib${lib_name}.lib"
    IMPORTED_LOCATION_RELEASE "${platform_spec_path}/release-lib/lib${lib_name}.lib"
  )
  set_target_properties(${target_name} PROPERTIES
    MAP_IMPORTED_CONFIG_MINSIZEREL Release
    MAP_IMPORTED_CONFIG_RELWITHDEBINFO Release
  )
else()
  set_target_properties(${target_name} PROPERTIES
    IMPORTED_LOCATION "${platform_spec_path}/lib${lib_name}.a"
  )
endif()
