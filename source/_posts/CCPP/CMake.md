---
title: CMake
mathjs: true
date: 2021-09-07 15:00:01
tags:
- c/cpp
- cmake
---

# 常用变量

- PROJECT_SOURCE_DIR：工程根目录
- PROJECT_BINARY_DIR：运行cmake所在的目录，例如我在build目下cmake，那该变量就而是build/
- PROJECT_NAME：project定义的工程名称
- CMAKE_CURRENT_SOURCE_DIR：当前处理CMakeLists.txt所在的路径
- CMAKE_CURRENT_BINARY_DIR：target编译目录
- CMAKE_CURRENT_LIST_DIR：CMakeList.txt的完整路径
- EXECUTABLE_OUTPUT_PATH：重新定义目标二进制可执行文件的存放位置
- LIBRARY_OUTPUT_PATH：重新定义目标链接库文件的存放位置

# 基本语法

```cmake
# 最小版本
cmake_minimum_required(VERSION 3.9)

# 工程名
project(project)

# 可执行文件
add_executable(${PROJECT_NAME} /src/main.cpp)

# C/C++版本
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 11)

# 自定义变量
set(cf_example_version "1.0")
set(cf_example_version "${PROJECT_SOURCE_DIR}")

# 编译选项
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")

# 配置文件配置
configuration_file

# 添加子目录
add_subdirectory(subdirectory)

# 生成library以及给library别名，默认static
add_library(${PROJECT_NAME} SHARED/STATIC src/include)
add_library(sub::lib1 ALIAS ${PROJECT_NAME})

# 关联文件
target_link_libraries(
	${PROJECT_NAME} INTERFACE/PUBLIC/PRIVATE sub::lib1 # 当lib1只有.h文件时
)

# include目录
target_include_directories(
	${PROJECT_NAME} PUBLIC/INTERFACE/PRIVATE src/include
)

# install
# binary
install(TARGETS cmake_examples_inst_bin DESTINATION bin)
# library
install (TARGETS cmake_examples_inst
    LIBRARY DESTINATION lib)
# header files
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ DESTINATION include)
# config file
install(FILES cmake-exmpales.conf DESTINATION etc)

# 根据变量生成文件
configure_file(ver.h.in ${PROJECT_SOURCE_DIR}/ver.h) # 使用${}, 例如 char *version = "${cf_example_version}"
configure_file(path.h.in ${PROJECT_SOURCE_DIR}/path.h @ONLY) # 使用@@，例如 char *path = "@cf_example_path@"
```

# 使用clang进行check

- 安装clang

- 创建好源文件

- 使用cmake编译生成

- 使用scan-build进行静态检测

  ```shell
  scan-build -o ./scanbuildout [cmake/make]
  ```

  
