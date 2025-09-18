# The Flutter tooling requires CMake 3.10 or later.
cmake_minimum_required(VERSION 3.10)

project(dartbuzz_library VERSION 0.0.1 LANGUAGES C CXX)

# --- FreeType ---
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../third_party/freetype
                 ${CMAKE_CURRENT_BINARY_DIR}/freetype
                 EXCLUDE_FROM_ALL)

# --- HarfBuzz ---
set(HB_HAVE_FREETYPE ON CACHE BOOL "" FORCE)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../third_party/harfbuzz
                 ${CMAKE_CURRENT_BINARY_DIR}/harfbuzz
                 EXCLUDE_FROM_ALL)

# --- dartbuzz native library ---
add_library(dartbuzz SHARED
    dartbuzz.c
)

set_target_properties(dartbuzz PROPERTIES
    PUBLIC_HEADER dartbuzz.h
    OUTPUT_NAME "dartbuzz"
)

target_compile_definitions(dartbuzz PUBLIC DART_SHARED_LIB)

target_include_directories(dartbuzz PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/../third_party/freetype/include
    ${CMAKE_CURRENT_SOURCE_DIR}/../third_party/harfbuzz/src
)

target_link_libraries(dartbuzz
    harfbuzz
    freetype
)

# --- Android-specific flags ---
if (ANDROID)
    # Support Android 15 16k page size
    target_link_options(dartbuzz PRIVATE "-Wl,-z,max-page-size=16384")
endif()