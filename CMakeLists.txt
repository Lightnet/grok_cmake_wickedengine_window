# Minimum CMake version required
cmake_minimum_required(VERSION 3.12)

# Project name
project(WickedEngineExample CXX)

# Set C++ standard to C++17
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Define source files
set(SOURCES
    src/main.cpp
)

# Add executable (WIN32 for Windows GUI application)
add_executable(${PROJECT_NAME} WIN32 ${SOURCES})

# Path to WickedEngine (relative to CMake source directory)
set(WICKED_ENGINE_DIR "${CMAKE_SOURCE_DIR}/deps/WickedEngine")

# Check if WickedEngine directory exists
if(NOT EXISTS "${WICKED_ENGINE_DIR}")
    message(FATAL_ERROR "WickedEngine not found at ${WICKED_ENGINE_DIR}. Please clone it into deps/.")
endif()

# Include WickedEngine headers
target_include_directories(${PROJECT_NAME} PRIVATE
    "${WICKED_ENGINE_DIR}/WickedEngine"
)

# Link against pre-built WickedEngine library
set(WICKED_ENGINE_BUILD_DIR "${WICKED_ENGINE_DIR}/build/x64/$<CONFIG>")
target_link_directories(${PROJECT_NAME} PRIVATE "${WICKED_ENGINE_BUILD_DIR}")
target_link_libraries(${PROJECT_NAME} PRIVATE WickedEngine_Windows)

# Additional Windows libraries
if(WIN32)
    target_link_libraries(${PROJECT_NAME} PRIVATE
        user32.lib
        gdi32.lib
        shell32.lib
    )
endif()

# Set output directory to default build directory
set_target_properties(${PROJECT_NAME} PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}"
)

# Copy runtime dependencies after build, respecting configuration
# Copy shaders
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    "${WICKED_ENGINE_DIR}/WickedEngine/shaders"
    "${CMAKE_BINARY_DIR}/$<CONFIG>/shaders"
    COMMENT "Copying WickedEngine shaders to output directory ($<CONFIG>)"
)

# Copy dxcompiler.dll
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${WICKED_ENGINE_DIR}/WickedEngine/dxcompiler.dll"
    "${CMAKE_BINARY_DIR}/$<CONFIG>/dxcompiler.dll"
    COMMENT "Copying dxcompiler.dll to output directory ($<CONFIG>)"
)

# Copy dxil.dll
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${WICKED_ENGINE_DIR}/WickedEngine/dxil.dll"
    "${CMAKE_BINARY_DIR}/$<CONFIG>/dxil.dll"
    COMMENT "Copying dxil.dll to output directory ($<CONFIG>)"
)