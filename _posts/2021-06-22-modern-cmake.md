---
title: "Modern Cmake常用指令"
categories: cmake 编译
tags: cmake 编译
---

## 构建目标(可执行程序/代码库/纯头文件库)

### 添加库

```cmake
add_library(<target> [STATIC | SHARED] [source1] [source2...])
```

### 添加接口库

```cmake
add_library(<target> INTERFACE)
```

一般用作纯头文件库或虚拟库(用来表示多个库集合), 把target_xxx_xxx(<target> xxx)指令相关的配置给暴露到外部, 下面有关于INTERFACE, PUBLIC, PRIVATE的解释

### 添加可执行文件

```cmake
add_executable(<target> [source1] [source2...])
```

## 添加依赖库和头文件路径到目标

### 添加头文件搜索路径

```cmake
target_include_directories(<target>
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```

### 添加依赖的子库

```cmake
target_link_libraries(<target>
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```

Modern Cmake推荐不要使用`link_libraries`, `include_directories`等不带`target_`前缀的命令扰乱项目依赖关系.

`INTERFACE`, `PUBLIC`, `PRIVATE`三个关键字就跟c++,java等面向对象语言中的概念类似, 举个例子:

在构建`target`时会链接到`public_item`目标和`private_item`目标且会继承它们的`PUBIC`和`INTERFACE`依赖, 但不会链接到`interface_item`目标

```cmake
target_link_libraries(target
  INTERFACE interface_item
  PUBLIC public_item
  PRIVATE private_item)
```

在构建`new_target`时会链接到`target`目标本身和`public_item`目标, `interface_item`目标且会继承它们的`PUBIC`和`INTERFACE`依赖, 但不会链接`private_item`目标.

```cmake
target_link_libraries(new_target
  PRIVATE target)
```

## 多目录构建

把子目录加入到构建过程中:

```cmake
add_subdirectory(directory)
```

此命令会把子目录中的目标暴露到调用位置，方便继续添加依赖，常规用法例子：

```cmake
add_subdirectory(dir_name)
target_link_libraries(${PROJECT_NAME} INTERFACE target1 target2) # target1和target2是dir_name目录中CMakelist.txt中的目标名
```

## 辅助调试

cmake隐藏了完整编译过程, 为了确定cmake配置是否正确, 偶尔看看完整的编译输出还是必要的.

使用ninja时:

```shell
cmake -G Ninja ..
ninja -v
```

使用cmake时:

```shell
cmake -G "Unix Makefiles" ..
make VERBOSE=1
```
