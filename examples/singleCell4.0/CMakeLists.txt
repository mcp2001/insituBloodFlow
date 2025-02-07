cmake_minimum_required(VERSION 2.8.12)
project(cellFlow)
enable_language(CXX)

set(PALABOS_ROOT "/home/tishn/myFork/insituBloodFlow/src/palabos/" CACHE STRING "")
set(BLOODFLOW_ROOT "/home/tishn/myFork/insituBloodFlow/BloodFlow/" CACHE STRING "")
set(LAMMPS_DIR "/home/tishn/myFork/insituBloodFlow/src/lammps/" CACHE STRING "")
set(LAMMPS_LIB "${LAMMPS_DIR}/src")
set(SENSEI_DIR "/home/tishn/myFork/insituBloodFlow/install/sensei/lib/cmake/" CACHE PATH "Path to SENSEI root directory")
set(SENSEI_INCLUDE "/home/tishn/myFork/insituBloodFlow/install/sensei/include/" CACHE PATH "")

# include_directories(${SENSEI_DIR}/include)

set (CMAKE_RUNTIME_OUTPUT_DIRECTORY "../")
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
    #set(CMAKE_CONFIGURATION_TYPES "Debug;Release")
endif()
message("Generated with config types: ${CMAKE_CONFIGURATION_TYPES}")
message(${CMAKE_BUILD_TYPE})

# find_package(SENSEI REQUIRED)
# #find_package(SENSEI)
# #find_package(sensei) #NT for SENSEI setup with most recent script.
# if(SENSEI_FOUND)
#   message("FOUND SENSEI")
# endif()


if(${CMAKE_CXX_COMPILER_ID} STREQUAL GNU)
    message("GCC.")
    set(CMAKE_CXX_FLAGS "-std=c++11 -Wall -Wnon-virtual-dtor")
    set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
    set(CMAKE_CXX_FLAGS_DEBUG "-g -DPLB_DEBUG -O0")
elseif(${CMAKE_CXX_COMPILER_ID} STREQUAL Clang)
    message("Clang.")
    set(CMAKE_CXX_FLAGS "-std=c++11 -Wall -Wnon-virtual-dtor")
    set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
    set(CMAKE_CXX_FLAGS_DEBUG "-g -DPLB_DEBUG -O0")
elseif(${CMAKE_CXX_COMPILER_ID} STREQUAL MSVC)
    message("MSVC.")
    set(CMAKE_CXX_FLAGS_RELEASE "/Ox /Ot /GS- /GL /DNDEBUG")
    set(CMAKE_CXX_FLAGS_DEBUG "/DPLB_DEBUG")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "/LTCG /INCREMENTAL:NO /OPT:REF")
else()
    message( FATAL_ERROR "CXX compiler not recognized. CMake will quit." )
endif()

message("------------------------------------ 0")
option(ENABLE_MPI "Enable MPI" ON)
if(ENABLE_MPI)
    message("Enabling MPI")
    find_package(MPI REQUIRED)
    message("------------------------------------ 1")
    if(MPI_CXX_FOUND)
        #set(CMAKE_CXX_COMPILER "${MPI_CXX_COMPILER}")
        include_directories(${MPI_CXX_INCLUDE_PATH})
        message("------------------------------------ 2")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${MPI_CXX_COMPILE_FLAGS}")
        add_definitions(-DPLB_MPI_PARALLEL)
        message("------------------------------------ 3")
    endif()
    message("------------------------------------ 4")
endif()

message("MPI COMPILE FLAGS: ${MPI_CXX_COMPILE_FLAGS}")
message("MPI LIBRARIES: ${MPI_CXX_LIBRARIES}")
message("CMAKE_CXX_COMPILER_ID: $CMAKE_CXX_COMPILER_ID")


if(WIN32)
    option(ENABLE_POSIX "Enable POSIX" OFF)
else()
    option(ENABLE_POSIX "Enable POSIX" ON)
endif()
if(ENABLE_POSIX)
    message("Enabling POSIX")
    add_definitions(-DPLB_USE_POSIX)
endif()
if(APPLE)
    add_definitions(-DPLB_MAC_OS_X)
endif()
if(WIN32 OR CYGWIN)
    add_definitions(-DPLB_WINDOWS)
endif()

set(sources cellFlow.cpp)
include_directories(${SENSEI_INCLUDE})
# set(libs sMPI sDIY sOPTS thread)

include_directories("${PALABOS_ROOT}/src")
include_directories("${PALABOS_ROOT}/externalLibraries")
include_directories("${PALABOS_ROOT}/externalLibraries/Eigen3")
include_directories("${BLOODFLOW_ROOT}/ibm")
include_directories("${BLOODFLOW_ROOT}/rbc")
# include_directories("${LAMMPS_LIB}")
file(GLOB_RECURSE PALABOS_SRC "${PALABOS_ROOT}/src/*.cpp")
file(GLOB_RECURSE EXT_SRC "${PALABOS_ROOT}/externalLibraries/tinyxml/*.cpp")
add_library(palabos STATIC ${PALABOS_SRC} ${EXT_SRC})
# add_library(mpicxxlib STATIC ${MPI_CXX_LIBRARIES})
# target_link_libraries(cellFlow ${MPI_CXX_LIBRARIES})

include_directories("${LAMMPS_LIB}")
# include_directories("${COUPLING_DIR}/src")
add_library(lammps STATIC IMPORTED)
set_property(TARGET lammps PROPERTY
             IMPORTED_LOCATION "${LAMMPS_LIB}/liblammps_mpi.a")

list(APPEND libs palabos lammps sensei) # ${MPI_CXX_LIBRARIES})
list(APPEND sources Bridge.cpp LPdataAdaptor.cpp)
# list(APPEND libs sensei)

add_executable(cellFlow ${sources})
target_link_libraries(cellFlow ${MPI_CXX_LIBRARIES})
target_link_libraries(cellFlow ${libs})


# if (OSCILLATOR_CUDA)
#   target_compile_definitions(oscillator PUBLIC OSCILLATOR_CUDA)
#   target_compile_features(oscillator PUBLIC cxx_std_17)
#   set_target_properties(oscillator PROPERTIES POSITION_INDEPENDENT_CODE ON)
#   set_target_properties(oscillator PROPERTIES CUDA_SEPARABLE_COMPILATION ON)
# endif ()


target_include_directories(cellFlow SYSTEM PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
# target_link_libraries(cellFlow ${MPI_CXX_LIBRARIES})
install(TARGETS cellFlow RUNTIME DESTINATION bin)
# return()
