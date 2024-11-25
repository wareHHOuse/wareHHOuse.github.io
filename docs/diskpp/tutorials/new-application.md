---
title: Creating a new DiSk++ application
parent: Getting started with DiSk++
nav_order: 1
---

# Tutorial 0: creating a new application

DiSk++ provides already a number of applications solving different problems, however you may be interested in adding your own application solving your specific problem.

Adding a new application can be done automatically or manually, depending on the level of customization you need.

## The automatic way

If you navigate to the `apps` folder of the DiSk++ source tree, you will notice a shell script named `newapp.sh`. That script takes a single parameter, the name of the application you want to create. If you want to create a new application named `myapp`, here is the command you need:

```sh
./newapp.sh myapp
```

This command does the following:
 * creates a directory named `myapp` under `apps` for your application
 * creates an empty C++ source file `myapp/src/myapp.cpp`
 * creates `myapp/CMakeLists.txt` with the build instructions for your new app
 * adds your new app to the top-level `CMakeLists.txt`

To start developing your DiSk++ application, just edit `myapp/src/myapp.cpp`. Once you are done, go to the build directory and type `make`.

## The manual way

In order to have some more control in the process of adding a new application to the DiSk++ tree, here you can find a summary of the steps taken automatically by `newapp.sh`.

In the DiSk++ source tree, the applications live under the `apps/` subdirectory. Therefore, the first step is to create a new directory for your application and its main source file. Assuming you are in the root of the DiSk++ source tree and you want to call your application `myapp`, you simply do

```
mkdir -p apps/myapp/src
touch apps/myapp/src/myapp.cpp
```

Then you need to create the `CMakeLists.txt` for your application. Its full path will be `apps/myapp/CMakeLists.txt`:

```
set(LINK_LIBS diskpp)

add_executable(myapp src/myapp.cpp)
target_link_libraries(myapp ${LINK_LIBS})
install(TARGETS myapp RUNTIME DESTINATION bin)
```

Finally, you need to add your application to the DiSk++ file `apps/CMakeLists.txt`

```
option(BUILD_APP_MYAPP "Build myapp application" ON)
if (BUILD_APP_MYAPP)
    add_subdirectory(myapp)
endif()
```
