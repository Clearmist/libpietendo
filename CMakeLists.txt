cmake_minimum_required(VERSION 3.12)

project(libpietendo
    VERSION 1.0.0
    LANGUAGES CXX C
)

# Set C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Platform detection
if(WIN32)
    set(PLATFORM_NAME "win32")
elseif(APPLE)
    set(PLATFORM_NAME "macos")
else()
    set(PLATFORM_NAME "linux")
endif()

# Output directories
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin/${PLATFORM_NAME})

if(WIN32)
    set(TOOLCHAIN_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../libtoolchain/build/Release/toolchain.lib)
    set(MBEDTLS_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../libmbedtls/build/Release/libmbedtls.lib)
    set(FMT_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../libfmt/build/Release/fmt.lib)
else()
    set(TOOLCHAIN_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../libtoolchain/bin/${PLATFORM_NAME}/libtoolchain.a)
    set(MBEDTLS_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../libmbedtls/bin/${PLATFORM_NAME}/libmbedtls.a)
    set(FMT_LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../libfmt/bin/${PLATFORM_NAME}/libfmt.a)
endif()

# Collect source files from all subdirectories
file(GLOB_RECURSE PIETENDO_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cc
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
)

# Create static library
add_library(pietendo STATIC ${PIETENDO_SOURCES})

# Include directories
target_include_directories(pietendo
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/deps/libtoolchain/include
    ${CMAKE_CURRENT_SOURCE_DIR}/deps/libmbedtls/include
    ${CMAKE_CURRENT_SOURCE_DIR}/deps/libfmt/include
)

# Link dependencies
target_link_libraries(pietendo
    PUBLIC
    ${TOOLCHAIN_LIB_PATH}
    ${MBEDTLS_LIB_PATH}
    ${FMT_LIB_PATH}
)

# Compiler flags
if(NOT MSVC)
    target_compile_options(pietendo PRIVATE -Wall -Wno-unused-value -Wno-unused-but-set-variable -fPIC)
    if(APPLE)
        target_compile_options(pietendo PRIVATE -Wno-unused-private-field)
    endif()
endif()
