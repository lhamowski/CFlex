# CFlex

A flexible CMake utility library for simplified build configuration management.

## Overview

CFlex provides a clean interface for managing compiler and linker flags across different build types and target scopes in CMake projects. It eliminates the complexity of manual flag management while ensuring consistent build configurations.

## Downloading

```
mkdir -p cmake
wget -O cmake/CFlex.cmake https://github.com/lhamowski/CFlex/releases/latest/download/CFlex.cmake
```

## Features

- **Scope-aware flag management**: Apply flags to PUBLIC, PRIVATE, INTERFACE, or GLOBAL scopes
- **Build-type specific configurations**: Set different flags for DEBUG, RELEASE, etc.
- **Automatic target configuration**: Flags are applied automatically when targets are created

### Setting Compiler Flags

```cmake
cflex_set_cxx_flags(
    PRIVATE 
        -Wall # Enable all warnings
        -Wextra # Enable extra warnings
        -Wpedantic # Enable pedantic warnings
)

cflex_set_cxx_flags(
    BUILD_TYPES Debug
    GLOBAL 
        -Werror # Treat warnings as errors
        -O0 # Disable optimizations
        -g # Generate debug information
)

cflex_set_cxx_flags(
    BUILD_TYPES Release
    GLOBAL
        -Werror # Treat warnings as errors
        -O3 # Enable optimizations
        -DNDEBUG # Disable assertions
)

cflex_set_cxx_flags(
    BUILD_TYPES Coverage
    GLOBAL
        -Werror # Treat warnings as errors
        -O0 # Disable optimizations
        -g # Generate debug information
    PRIVATE
        --coverage # Enable coverage
)
```

### Setting Linker Flags

```cmake
cflex_set_cxx_flags(
    BUILD_TYPES UBSan
    GLOBAL
        -O1 # Enable optimizations level 1
        -g # Generate debug information
        -DNDEBUG # Disable assertions
        -fsanitize=address # Enable address sanitizer
        -fno-omit-frame-pointer # Keep frame pointers
)

cflex_set_linker_flags(
    GLOBAL
        -Wl,--exclude-libs=ALL # It specifies a list of archive libraries from which symbols should not be automatically exported
        -Wl,--as-needed # Only link libraries that are needed
        -Wl,-z,defs # Ensure that all symbols are defined
)

cflex_set_linker_flags(
    BUILD_TYPES Coverage
    PRIVATE
        --coverage # Enable coverage
)
```

### Creating Targets

```cmake
# Create a static library (default)
cflex_add_library(mylib
    SOURCES src/lib.cpp src/utils.cpp
)

# Create a shared library
cflex_add_library(mysharedlib SHARED
    SOURCES src/shared.cpp
)

# Create an executable
cflex_add_executable(myapp
    SOURCES src/main.cpp
)
```

## API Reference

### Flag Management Functions

#### `cflex_set_c_flags([BUILD_TYPES <types>...] [PUBLIC <flags>...] [PRIVATE <flags>...] [INTERFACE <flags>...] [GLOBAL <flags>...])`
Set C compiler flags.

#### `cflex_set_cxx_flags([BUILD_TYPES <types>...] [PUBLIC <flags>...] [PRIVATE <flags>...] [INTERFACE <flags>...] [GLOBAL <flags>...])`
Set C++ compiler flags.

#### `cflex_set_linker_flags([BUILD_TYPES <types>...] [PUBLIC <flags>...] [PRIVATE <flags>...] [INTERFACE <flags>...] [GLOBAL <flags>...])`
Set linker flags for all target types.

#### `cflex_set_exe_linker_flags(...)`
Set linker flags specifically for executable targets.

#### `cflex_set_shared_linker_flags(...)`
Set linker flags specifically for shared library targets.

#### `cflex_set_static_linker_flags(...)`
Set linker flags specifically for static library targets.

#### `cflex_set_module_linker_flags(...)`
Set linker flags specifically for module targets.

### Target Creation Functions

#### `cflex_add_library(<target> [STATIC|SHARED|MODULE] SOURCES <sources>...)`
Create a library target with automatic CFlex configuration.

#### `cflex_add_executable(<target> SOURCES <sources>...)`
Create an executable target with automatic CFlex configuration.

#### `cflex_configure_target(<target>)`
Manually apply CFlex configurations to an existing target.

## Configuration Options

- `CFLEX_LOG_BUILD_OPTIONS`: Enable logging of applied build options (default: ON)

## Requirements

- CMake 3.13 or higher
