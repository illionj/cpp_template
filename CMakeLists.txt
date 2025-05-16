cmake_minimum_required(VERSION 3.12)
# set(CMAKE_C_COMPILER   /usr/bin/clang-14)
# set(CMAKE_CXX_COMPILER /usr/bin/clang++-14)
project(coroutine VERSION 0.1 LANGUAGES  CXX)
set(CMAKE_CONFIGURATION_TYPES "RelWithDebInfo;Debug;Release;MinSizeRel" CACHE STRING "" FORCE)
if (NOT CMAKE_BUILD_TYPE OR CMAKE_BUILD_TYPE STREQUAL "None")
    set(CMAKE_BUILD_TYPE "RelWithDebInfo")
    message(STATUS "CMAKE_BUILD_TYPE is not set,set to default = ${CMAKE_BUILD_TYPE}")
endif()
set(CMAKE_BUILD_TYPE "Debug")
message(STATUS "CMAKE_BUILD_TYPE: ${CMAKE_BUILD_TYPE}")




option(OVERDIDE_CXX_NEW_DELETE "Override C++ new/delete operator" OFF)
option(SAVE_ALLOCATE_SIZE "Use more menory to save allocate size" OFF)




set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fcoroutines-ts")
    set(CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS} -latomic")
    add_link_options(-fuse-ld=lld)
    # Do not build with libc++ (LLVM's implementation of the C++ standard library) in fdb
    set(USE_LIBCXX OFF)
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fcoroutines")
endif()

include(cmake/Sanitizers.cmake)
include(cmake/CompileFlags.cmake)




store_compile_flags()
add_subdirectory("third_party/fmt" EXCLUDE_FROM_ALL)
restore_compile_flags()
set(ZSTD_BUILD_STATIC ON)
add_subdirectory("third_party/zstd/build/cmake" EXCLUDE_FROM_ALL)
set(zstd_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/third_party/zstd/lib")
set(ZSTD_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/third_party/zstd/lib")
set(zstd_FOUND ON)
set(ZSTD_FOUND ON)
set(zstd_LIBRARIES "${PROJECT_BINARY_DIR}/third_party/zstd/build/cmake/lib/libzstd.a")
set(ZSTD_LIBRARY "${PROJECT_BINARY_DIR}/third_party/zstd/build/cmake/lib/libzstd.a")
restore_compile_flags()
add_subdirectory("third_party/googletest" EXCLUDE_FROM_ALL)
restore_compile_flags()
set(FOLLY_NO_EXCEPTION_TRACER ON)
add_subdirectory("third_party/folly" EXCLUDE_FROM_ALL)
restore_compile_flags()


set (CMAKE_CXX_FLAGS                     "${CMAKE_CXX_FLAGS} ${COMPILER_FLAGS}")
set (CMAKE_CXX_FLAGS_RELWITHDEBINFO      "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} -O3 ${DEBUG_INFO_FLAGS} ${CMAKE_CXX_FLAGS_ADD}")
set (CMAKE_CXX_FLAGS_DEBUG               "${CMAKE_CXX_FLAGS_DEBUG} -O0 ${DEBUG_INFO_FLAGS} -fno-inline ${CMAKE_CXX_FLAGS_ADD}")

set (CMAKE_C_FLAGS                       "${CMAKE_C_FLAGS} ${COMPILER_FLAGS} ${CMAKE_C_FLAGS_ADD}")
set (CMAKE_C_FLAGS_RELWITHDEBINFO        "${CMAKE_C_FLAGS_RELWITHDEBINFO} -O3 ${DEBUG_INFO_FLAGS} ${CMAKE_C_FLAGS_ADD}")
set (CMAKE_C_FLAGS_DEBUG                 "${CMAKE_C_FLAGS_DEBUG} -O0 ${DEBUG_INFO_FLAGS} -fno-inline ${CMAKE_C_FLAGS_ADD}")

set (CMAKE_ASM_FLAGS                     "${CMAKE_ASM_FLAGS} ${COMPILER_FLAGS} ${CMAKE_ASM_FLAGS_ADD}")
set (CMAKE_ASM_FLAGS_RELWITHDEBINFO      "${CMAKE_ASM_FLAGS_RELWITHDEBINFO} -O3 ${DEBUG_INFO_FLAGS} ${CMAKE_ASM_FLAGS_ADD}")
set (CMAKE_ASM_FLAGS_DEBUG               "${CMAKE_ASM_FLAGS_DEBUG} -O0 ${DEBUG_INFO_FLAGS} -fno-inline ${CMAKE_ASM_FLAGS_ADD}")


include(cmake/Target.cmake)

find_package(Threads REQUIRED)
find_package(Boost  REQUIRED COMPONENTS filesystem system program_options)

# set(ENABLE_CLANG_TIDY ON)
# include(cmake/CLangTidy.cmake)

add_subdirectory(src)




