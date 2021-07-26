cmake_minimum_required(VERSION 3.1)
project(Manifold)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -pthread")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG}")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")  # enable assert
set(CMAKE_LINKER_FLAGS "${CMAKE_LINKER_FLAGS}")
set(CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_LINKER_FLAGS_DEBUG}")

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-int-in-bool-context -Wsign-compare")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -fsanitize=address")
    set(CMAKE_LINKER_FLAGS "${CMAKE_LINKER_FLAGS}")
    set(CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_LINKER_FLAGS_DEBUG} -fsanitize=address")
endif()

option(BUILD_PERFORMANCE_TEST "More subdivisition for performance test" OFF)
option(BUILD_OPENMP "Enable support for OpenMP" OFF)
option(BUILD_LOG "Enable verbose log" OFF)

if(BUILD_PERFORMANCE_TEST)
    add_definitions(-DPERFORMANCE_TEST)
endif(BUILD_PERFORMANCE_TEST)

if(BUILD_OPENMP)
    add_definitions(-DWITH_OMP)
endif(BUILD_OPENMP)

if(BUILD_LOG)
    add_definitions(-DLOG_OUTPUT)
endif(BUILD_LOG)

set(
    manifold_SRC
    src/BVH.cpp
    src/Intersection.cpp
    src/Model_OBJ.cpp
)

if (BUILD_SHARED_LIBS)
    add_library(manifold SHARED ${manifold_SRC})
else()
    add_library(manifold STATIC ${manifold_SRC})
endif()

target_include_directories(manifold PUBLIC include)
target_include_directories(manifold PUBLIC 3rd)

add_executable(manifold-bin src/main.cpp)
set_target_properties(manifold-bin PROPERTIES OUTPUT_NAME manifold)
target_link_libraries(manifold-bin PRIVATE manifold)
# target_link_libraries(manifold-bin PRIVATE 3rd)

set(
    simplify_SRC
    src/simplify.cpp
)

add_executable(
    simplify
    ${simplify_SRC}
)
target_include_directories(simplify PRIVATE 3rd)

