cmake_minimum_required(VERSION 2.6 FATAL_ERROR)

project(minisat-lib)

#--------------------------------------------------------------------------------------------------
# Configurable options:

option(STATIC_BINARIES "Link binaries statically." ON)
option(USE_SORELEASE   "Use SORELEASE in shared library filename." ON)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib )
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib )
set( CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/lib )

file(COPY 
	minisat/mtl 
	minisat/utils 
	minisat/core 
	minisat/simp
    DESTINATION 
	include/minisat
    FILES_MATCHING PATTERN "*.h")

#--------------------------------------------------------------------------------------------------
# Library version:

set(MINISAT_SOMAJOR   2)
set(MINISAT_SOMINOR   2)
set(MINISAT_SORELEASE 0)

# Compute VERSION and SOVERSION:
if (USE_SORELEASE)
  set(MINISAT_VERSION ${MINISAT_SOMAJOR}.${MINISAT_SOMINOR}.${MINISAT_SORELEASE})
else()
  set(MINISAT_VERSION ${MINISAT_SOMAJOR}.${MINISAT_SOMINOR})
endif()
set(MINISAT_SOVERSION ${MINISAT_SOMAJOR})

#--------------------------------------------------------------------------------------------------
# Compile flags:

add_definitions(-D__STDC_FORMAT_MACROS -D__STDC_LIMIT_MACROS)

#--------------------------------------------------------------------------------------------------
# Build Targets:

set(MINISAT_LIB_SOURCES
	minisat/mtl/Alg.h
	minisat/mtl/Alloc.h
	minisat/mtl/Heap.h
	minisat/mtl/IntMap.h
	minisat/mtl/IntTypes.h
	minisat/mtl/Map.h
	minisat/mtl/Queue.h
	minisat/mtl/Rnd.h
	minisat/mtl/Sort.h
	minisat/mtl/Vec.h
	minisat/mtl/XAlloc.h
	minisat/core/SolverTypes.h
	minisat/core/Solver.h
	minisat/simp/SimpSolver.h
	minisat/utils/Options.h
	minisat/utils/System.h

    minisat/core/Solver.cc
    minisat/simp/SimpSolver.cc
    minisat/utils/System.cc)

add_library(minisat-lib-static STATIC ${MINISAT_LIB_SOURCES})
add_library(minisat-lib-shared SHARED ${MINISAT_LIB_SOURCES})

set_target_properties(minisat-lib-static 
	PROPERTIES 
	OUTPUT_NAME "minisat")
	
set_target_properties(minisat-lib-shared
	PROPERTIES
    OUTPUT_NAME "minisat" 
    VERSION ${MINISAT_VERSION}
    SOVERSION ${MINISAT_SOVERSION})

target_include_directories(minisat-lib-static PUBLIC
	${CMAKE_CURRENT_SOURCE_DIR}
)

target_include_directories(minisat-lib-shared PUBLIC
	${CMAKE_CURRENT_SOURCE_DIR}
)

