# WickedEngineExample

A simple example project using the WickedEngine game engine.

# Information:
  Note this is testing build for Grok agent AI help build for cmake.

## Requirements

- **Operating System**: Windows 10 or later
- **Visual Studio 2022**: Community, Professional, or Enterprise edition with C++ Desktop Development workload
- **CMake**: Version 3.12 or higher ([Download](https://cmake.org/download/))
- **Git**: For downloading WickedEngine ([Download](https://git-scm.com/downloads))
- **DirectX**: Included with Windows SDK (installed with Visual Studio)

## Setup Instructions

# Set Up (test):

```
mkdir deps
cd deps
git clone https://github.com/turanszkij/WickedEngine.git
```

```
mkdir build
cd build

cmake -G "Visual Studio 17 2022" ..
// or
cmake ..
```
```
cmake --build . --config Release
```
```
cmake --build . --config Debug
```

# build.bat (Main):
```
@echo off
echo Building WickedEngineExample and its dependencies...

REM Set paths
set WICKED_ENGINE_DIR=deps\WickedEngine
set BUILD_DIR=build
set WICKED_REPO_URL=https://github.com/turanszkij/WickedEngine.git
set CONFIG=%1

REM Default to Debug if no configuration specified
if "%CONFIG%"=="" (
    set CONFIG=Debug
    echo No configuration specified. Defaulting to Debug.
) else (
    if /I NOT "%CONFIG%"=="Debug" if /I NOT "%CONFIG%"=="Release" (
        echo Error: Invalid configuration '%CONFIG%'. Use 'Debug' or 'Release'.
        exit /b 1
    )
)

REM Check if git is installed
where git >nul 2>&1
if %ERRORLEVEL% NEQ 0 (
    echo Error: Git is not installed or not in PATH. Please install Git to download WickedEngine.
    exit /b 1
)

REM Check if WickedEngine directory exists, clone it if not
if not exist %WICKED_ENGINE_DIR% (
    echo %WICKED_ENGINE_DIR% not found. Downloading WickedEngine repository...
    mkdir deps 2>nul
    git clone %WICKED_REPO_URL% %WICKED_ENGINE_DIR%
    if %ERRORLEVEL% NEQ 0 (
        echo Failed to clone WickedEngine repository.
        exit /b %ERRORLEVEL%
    )
    echo Successfully downloaded WickedEngine to %WICKED_ENGINE_DIR%.
) else (
    echo %WICKED_ENGINE_DIR% already exists. Skipping download.
)

REM Navigate to WickedEngine directory
cd %WICKED_ENGINE_DIR%

REM Build only WickedEngine_Windows (excludes Tests and ImGui examples)
echo Building WickedEngine (%CONFIG%) - Core library only...
"C:\Program Files\Microsoft Visual Studio\2022\Community\MSBuild\Current\Bin\MSBuild.exe" WickedEngine.sln /p:Configuration=%CONFIG% /p:Platform=x64 /t:WickedEngine_Windows
if %ERRORLEVEL% NEQ 0 (
    echo Failed to build WickedEngine.
    cd ..\..
    exit /b %ERRORLEVEL%
)
echo Successfully built WickedEngine core library.

REM Return to project root
cd ..\..

REM Create build directory if it doesn't exist
if not exist %BUILD_DIR% mkdir %BUILD_DIR%

REM Navigate to build directory
cd %BUILD_DIR%

REM Configure your project with CMake
cmake -G "Visual Studio 17 2022" -A x64 ..

REM Build your project
cmake --build . --config %CONFIG%

REM Check if build succeeded
if %ERRORLEVEL% EQU 0 (
    echo Build succeeded!
    echo Executable is located at: %CD%\%CONFIG%\WickedEngineExample.exe
    echo Running the application...
    %CONFIG%\WickedEngineExample.exe
) else (
    echo Build failed with error code %ERRORLEVEL%.
    exit /b %ERRORLEVEL%
)

REM Return to project root
cd ..

echo Done.
pause
```

```
build.bat Debug
```
```
build.bat Release
```
# The script will:
 * Download WickedEngine to deps/WickedEngine if not present.
 * Build only the core WickedEngine library (excluding tests and ImGui examples) using MSBuild.
 * Build your project using CMake.
 * Run the executable if successful.

# Output:
 * Executable: build/Debug/WickedEngineExample.exe (or build/Release/ for Release)
 * Dependencies: shaders/, dxcompiler.dll, dxil.dll copied to the output directory.