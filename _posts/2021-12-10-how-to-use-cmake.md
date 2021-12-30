---
layout: post
title: How To Use CMake
date: 2021-12-10 13:32:20 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: i-rest.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [Holidays, Hawaii]
---

CMake是一款优秀的工程构建工具，全称是Cross Platform Make，起初是为了满足跨平台的需求，而后不断完善并广泛使用(KDE开发者使用了近10年的autotools，在KDE4时改用CMake)。

### 特点及优势
- 开放源代码，具有BSD许可
- 跨平台，支持linux，mac，windows等常用的不同操作系统
- 编译语言简单，易用，简化编译构建过程
- 编程高效（比autotools快40%），可扩展（ros中的catkin，ament，colcon都是基于cmake构建的）

### 常用指令

* cmake --build ./build --config Debug --target clean -j 10
* cmake --no-warn-unused-cli -DCMAKE_EXPORT_COMPILE_COMMANDS:BOOL=TRUE -H./  -B./build -G "Visual Studio 16 2019" -T host=x64 -A win32
* cmake --build ./build --config Debug --target Async++ -j 10

### CMake Manual

一、变量的引用方式是使用“${}”，在IF中，不需要使用这种方式，直接使用变量名即可

二、自定义变量使用SET(OBJ_NAME xxxx)，使用时${OBJ_NAME}

三、cmake的常用变量：
CMAKE_BINARY_DIR, PROJECT_BINARY_DIR ：这两个变量内容一致，如果是内部编译，就指的是工程的顶级目录，如果是外部编译，指的就是工程编译发生的目录。
CMAKE_SOURCE_DIR, PROJECT_SOURCE_DIR：这两个变量内容一致，都指的是工程的顶级目录。
CMAKE_CURRENT_BINARY_DIR：外部编译时，指的是target目录，内部编译时，指的是顶级目录
CMAKE_CURRENT_SOURCE_DIR：CMakeList.txt所在的目录
CMAKE_CURRENT_LIST_DIR：CMakeList.txt的完整路径
CMAKE_CURRENT_LIST_LINE：当前所在的行
CMAKE_MODULE_PATH：如果工程复杂，可能需要编写一些cmake模块，这里通过SET指定这个变量
LIBRARY_OUTPUT_DIR, BINARY_OUTPUT_DIR：库和可执行的最终存放目录
PROJECT_NAME, CMAKE_PROJECT_NAME：前者是当前CMakeList.txt里设置的project_name，后者是整个项目配置的project_name

四、cmake中调用环境变量

```
1. Using $ENV{NAME} : 调用系统环境变量，我们也可以使用 "SET(ENV{NAME} value)".  需要注意的是这里"ENV"没有"$".
2. CMAKE_INCLUDE_CURRENT_DIR 等同于 INCLUDE_DIRECTORY(${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR})
```

五、其他的内置变量

```
1. BUILD_SHARED_LIBS: set the default value when using ADD_LIBRARY()
2. CMAKE_C_FLAGS: set compiler for c language
3. CMAKE_CXX_FLAGS: set compiler for c++ language
```

六、区分debug和release

在工程build目录下执行 cmake .. -DCMAKE_BUILD_TYPE=DEBUG|RELEASE，再执行make， 或者在顶级CMakeList.txt里加入: set(CMAKE_BUILD_TYPE Debug|Release|MinSizeRel|RelWithDebInfo)

七、指定编译32bit或64bit程序

```
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -m32")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -m32")
```

八、通过cmake打印能获取到的所有系统环境变量

```
# 在CMakeList.txt中使用
execute_process(
  COMMAND ${CMAKE_COMMAND} -E environment
)

# 在终端中使用
cmake -E environment
```

九、设置install路径

```
// 命令行里设置
cmake -DCMAKE_INSTALL_PREFIX=&lt;install_path&gt;

// 或者在根cmake里设置
SET(CMAKE_INSTALL_PREFIX &lt;install_path&gt;)
```

默认安装路径是/usr/local

十、获取绝对路径和父目录

```
# 获取文件绝对路径
get_filename_component(FULL_NAME "${FILE}" ABSOLUTE)

# 获取文件父路径
get_filename_component(PARENT_DIR "${FULL_NAME}" PATH)
```

十一、判断操作系统平台及Win是否为32位

```
if(CMAKE_SYSTEM_NAME MATCHES "Linux")  // 注意区分大写
  message(STATUS "Linux platorm!")
elseif(CMAKE_SYSTEM_NAME MATCHES "Windows")
if(CMAKE_CL_64)
  message(STATUS "Windows Win64 platform!")
  else()
    message(STATUS "Windows Win32 platform!")
  endif(CMAKE_CL_64)
elseif(CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
  message(STATUS "FreeBSD platform!")
else()
  message(STATUS "other platform!")
endif(CMAKE_SYSTEM_NAME MATCHES "Linux")
```

十二、生成VS工程后
ZERO_CHECK
该目标会检查生成工程的 CMake 配置文件（ CMakeLists.txt ）是否更新。如更新，将运行 CMake 重新生成工程文件。

如果确信 CMakeLists.txt 不会被更新，或者希望手工运行 CMake 重新生成工程文件，可以在 CMakeLists.txt 配置文件中添加 set(CMAKE_SUPPRESS_REGENERATION FALSE) 命令， ZERO_CHECK 目标将不会生成。

ALL_BUILD
该目标会导致工程中所有项目被构建，类似 Visual Studio 的 Build All 或者 make 的 make all命令。

![I and My friends]({{site.baseurl}}/assets/img/we-in-rest.jpg)
