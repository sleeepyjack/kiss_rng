cmake_minimum_required(VERSION 3.18 FATAL_ERROR)

project(hpc_helpers VERSION 0.0.1 LANGUAGES CXX CUDA)

###################################################################################################
# - build type ------------------------------------------------------------------------------------

# Set a default build type if none was specified
set(DEFAULT_BUILD_TYPE "Release")

###################################################################################################
# - find packages we depend on --------------------------------------------------------------------

find_package(CUDAToolkit 9.0 REQUIRED)

# package manager
include(cmake/CPM.cmake)

# timers, utils, etc.
# TODO switch to default branch once merged
CPMAddPackage(
  NAME helpers
  URL https://gitlab.rlp.net/pararch/hpc_helpers/-/archive/restructure/hpc_helpers-restructure.zip
)

###################################################################################################
# - define target   -------------------------------------------------------------------------------
add_library(kiss_rng INTERFACE)
target_include_directories(kiss_rng INTERFACE
                           "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
                           $<INSTALL_INTERFACE:include>)
target_link_libraries(kiss_rng INTERFACE helpers CUDA::cudart)

###################################################################################################
# - build options ---------------------------------------------------------------------------------

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_C_COMPILER $ENV{CC})
set(CMAKE_CXX_COMPILER $ENV{CXX})
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} --expt-extended-lambda --expt-relaxed-constexpr")

option(KISS_RNG_BUILD_EXAMPLES "Configure CMake to build the example" OFF)
if(KISS_RNG_BUILD_EXAMPLES)
    add_executable(example.out "${CMAKE_CURRENT_SOURCE_DIR}/example.cu")
    target_link_libraries(example.out PRIVATE kiss_rng)
endif(KISS_RNG_BUILD_EXAMPLES)
