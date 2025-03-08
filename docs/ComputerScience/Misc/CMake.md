---
    comments: true
    statistics: true
---

# CMake

!!! abstract
    这是我跟着[官方教程](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)初学 CMake 时做的一点笔记，权当作一个备忘录和参考。

!!! info "参考链接"
    [CMake 官方文档](https://cmake.org/cmake/help/latest/index.html)

## 常见命令

- 指定 CMake 的最低版本要求

    ```cmake
    cmake_minimum_required(VERSION <version>)
    ```

- 定义项目的名称和使用的编程语言以及版本：

    ```cmake
    project(<project_name> [<language> ...] [VERSION <major>[.<minor>]])
    ```

    例如：

    ```cmake
    project(MyProject CXX VERSION 1.0)
    ```

- 指定要生成的可执行文件和其源文件：

    ```cmake
    add_executable(<target> <source_files>...)
    ```

- 添加头文件搜索路径：

    ```cmake
    include_directories(<dir>...)
    ```

- 设置变量的值：

    ```cmake
    set(<variable> <value>)
    ```

- 设置目标属性：

    ```cmake
    target_include_directories(TARGET target_name
                            [BEFORE | AFTER]
                            [SYSTEM] [PUBLIC | PRIVATE | INTERFACE]
                            [items1...])
    ```

- 条件语句 (if, elseif, else, endif 命令)：

    ```cmake
    if(<condition>)
        # commands
    elseif(<condition>)
        # commands
    else()
        # commands
    endif()
    ```

- 添加子目录：

    ```cmake
    add_subdirectory(<dir>)
    ```

## 备忘笔记

随手记一些跟着官方 tutorial 做的时候觉得有用的笔记。







