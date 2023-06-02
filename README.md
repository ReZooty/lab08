# lab08 by Telepov Igor

# Изменил CPackConfig.cmake
```sh
include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_CONTACT telepov.igor2013@yandex.ru)
set(CPACK_PACKAGE_VERSION_MAJOR ${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK ${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION ${PRINT_VERSION})

set(CPACK_RESOURCE_FILE_LICENSE ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README ${CMAKE_CURRENT_SOURCE_DIR}/README.md)

set(CPACK_RPM_PACKAGE_NAME "solver_lab")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "solver")
set(CPACK_RPM_PACKAGE_VERSION CPACK_PACKAGE_VERSION)

set(CPACK_DEBIAN_PACKAGE_NAME "libsolver-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_VERSION CPACK_PACKAGE_VERSION)

include(CPack)
```

# Изменил CMakeLists.txt
```sh
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(solver_package)

include_directories("formatter_lib/src")
include_directories("formatter_ex_lib/src")
include_directories("solver_lib/src")

add_library(formatter_lib STATIC "formatter_lib/src/formatter.cpp")
add_library(formatter_ex_lib STATIC "formatter_ex_lib/src/formatter_ex.cpp")
add_library(solver_lib STATIC "solver_lib/src/solver.cpp")

add_executable(solver "solver_application/src/equation.cpp")

target_link_libraries(solver solver_lib formatter_ex_lib formatter_lib)

include(CPackConfig.cmake)
```

# Изменил Cl.yml
```sh
name: CMake

on:
 push:
  branches: [master]
  tags: -"v0.1.*.*"
 pull_request:
  branches: [master]
env:
  BUILD_TYPE: Release
jobs: 
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Packing
      run: |
        cmake -H. -B_build
        cmake --build _build
        cd _build
        cpack -G "TGZ"
        cpack -G "DEB"
        cpack -G "RPM"
        cd ..
        cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
        cmake --build _build --target package
             
    - name: Moving
      run: |
        mkdir ../artifacts
        mv _build/*.tar.gz ../artifacts/
        mv _build/*.deb ../artifacts/
        mv _build/*.rpm ../artifacts/

    - name: Publish
      uses: actions/upload-artifact@v2
      with:
        name: artifact
        path: artifacts/
```

# Затем выгрузил на GitHub
```sh
$ git add .
$ git commit -m"final version"
$ git tag v0.1.0.2
$ git push origin master --tags

```

