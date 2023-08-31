cmake_minimum_required (VERSION 3.4.0)

option(VENDOR "One of [Intel|Nvidia|AMD]. This option enables users to specify the vendor of the target device to generate the binary for" Intel)

set(CMAKE_CXX_COMPILER "icpx")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsycl")

if (VENDOR STREQUAL "Nvidia" OR VENDOR STREQUAL "nvidia")
        set(CMAKE_CXX_COMPILER "clang++")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsycl-targets=nvptx64-nvidia-cuda")
elseif (VENDOR STREQUAL "AMD" OR VENDOR STREQUAL "amd")
        set(CMAKE_CXX_COMPILER "clang++")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsycl-targets=amdgcn-amd-amdhsa --offload-arch=gfx906")
elseif (NOT (VENDOR STREQUAL "Intel" OR VENDOR STREQUAL "intel"))
        message(ERROR "Invalid value set for VENDOR=${VENDOR}. Choose one of [Intel|Nvidia|AMD].")
endif ()

message(STATUS "CMAKE_CXX_COMPILER: ${CMAKE_CXX_COMPILER}")
message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")

project (guided_convolutionSeparable_SYCLmigration)
# Set default build type to RelWithDebInfo if not specified
if (NOT CMAKE_BUILD_TYPE)
        message (STATUS "Default CMAKE_BUILD_TYPE not set using Release with Debug Info")
        set (CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE
             STRING "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel"
             FORCE)
endif ()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

option (BUILD_SYCL_MIGRATED_SUBPROJECT "Build targets from subproject dpct_output" ON)
option (BUILD_SYCL_MIGRATED_OPTIMIZED_SUBPROJECT "Build targets from subproject convolutionSeparable_migrated_optimized" ON)

if (BUILD_SYCL_MIGRATED_SUBPROJECT)
        add_subdirectory (01_dpct_output)
endif ()
if (BUILD_SYCL_MIGRATED_OPTIMIZED_SUBPROJECT)
        add_subdirectory (02_sycl_migrated_optimized)
endif ()

