---
title: "CMake Guide"
---

In this guide, I shall document the bare minimum required for configuring a [CMake](https://cmake.org) project for buliding C/C++ projects and explain its core features so I don't ever have to go watch 20-minute+ long YouTube CMake tutorials again. I simply don't have the patience. I normally use [GNU Make](https://www.gnu.org/software/make/) for small simple C/C++ projects; it is simply and gets the job done. CMake is the de-facto standard in the industry for more complex C/C++ projects with external dependencies and more complicated build setups.

There are newer build tools like [Meson Build](https://mesonbuild.com), but the prevalent dominance of CMake in many popular open-source projects keeps it as the first-choice for many despite steep learning-curve. For many developers, it is just easier to coalesce around CMake rather than taking on more work for learning how to import CMake projects in a Meson Build setup and troubleshooting issues arising from doing so.

I must warn you that this guide is not for everyone. It is limited in scope _and_ depth for _my_ own use in my pet projects.

### header

CMake **`CMakeLists.txt`** project file typically contains these "header" commands:

```cmake
cmake_minimum_required(VERSION 3.27)

project(my_proj_name
        VERSION 1.2.3
        LANGUAGES C)
```

- [cmake_minimum_required](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html) dictates which CMake features are available and deprecated
- [project(<PROJECT_NAME> [VERSION] [LANGUAGES] ...)](https://cmake.org/cmake/help/latest/command/project.html) —
    - `<PROJECT_NAME>` _(REQUIRED)_ sets [PROJECT_NAME](https://cmake.org/cmake/help/latest/variable/PROJECT_NAME.html#variable:PROJECT_NAME) and _**top-level**_ [CMAKE_PROJECT_NAME](https://cmake.org/cmake/help/latest/variable/CMAKE_PROJECT_NAME.html#variable:CMAKE_PROJECT_NAME) in multi-level directory project
    - `[VERSION]` _(OPTIONAL)_ sets `<major>[.<minor>[.<patch>[.<tweak>]]]` sets variables for version number
    - `[LANGUAGES]` _(OPTIONAL)_ specifies which programming languages are needed to build the project

### simple project

Assume a simple project for now.

```text
my-project-root
|
+-- include
|   |-- my_code.h
|
+-- src
|   |-- main.c
|   |-- my_code.c
|
+-- CMakeLists.txt
```

```cmake
cmake_minimum_required(VERSION 3.27)

project(my_proj_name
        VERSION 1.2.3
        LANGUAGES C)

add_library(mylib
        src/my_code.c)

target_include_directories(mylib PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include

add_executable(main
        src/main.c)

# add dependency on libm and mylib
target_link_libraries(main PRIVATE
        m
        mylib)
```

#### build

Refer to [cmake](https://cmake.org/cmake/help/latest/manual/cmake.1.html) Command-Line Tool documentation for details

- **[Generate a Project Buildsystem](https://cmake.org/cmake/help/latest/manual/cmake.1.html#generate-a-project-buildsystem)**
    - `-G` specifies which build system generator to use (e.g., **Ninja**, **GNU Make**, etc...)
    - `-S` specifies path to root directory of the CMake project to build.
    - `-B` specifies path to directory which CMake will use as the root of build directory.
    - `-D` specifies CMake cache entries such as [CMAKE_BUILD_TYPE](https://cmake.org/cmake/help/latest/variable/CMAKE_BUILD_TYPE.html) for build types: `Debug`, `Release`, `RelWithDebInfo`, and `MinSizeRel`

    ```shell
    #
    # Assumes current directory is at my-project-root
    #
    # creates build directories for Debug and Release builds
    $ cmake -G Ninja -S . -B cmake-build-debug   -DCMAKE_BUILD_TYPE=Debug
    $ cmake -G Ninja -S . -B cmake-build-release -DCMAKE_BUILD_TYPE=Release
    ```

- **[Build a Project](https://cmake.org/cmake/help/latest/manual/cmake.1.html#build-a-project)**

    ```shell
    # build
    $ cmake --build cmake-build-debug
    $ cmake --build cmake-build-release
    ```

- **Running the Executable**
    Once built successfully, the build will produce **`main`** executable as specified by `add_executable(main ...)`

    ```shell
    $ ./cmake-build-debug/main

    $ ./cmake-build-release/main
    ```
