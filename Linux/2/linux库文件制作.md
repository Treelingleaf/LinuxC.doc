# linux库文件制作

## 一、课程介绍

- 库文件的作用（理解）

- 概念(了解)
- 分类(了解)
- 命名(理解)
- 制作与使用(熟练)

## 二、概述

### 1、为什么需要

**库文件的作用包括代码重用、提高程序效率、简化代码维护、便于模块化更新和跨平台开发。**

* 代码重用。库文件包含常用的函数、类或模块，可以方便地被不同的应用程序重复使用，从而减少代码量，提高代码的可维护性和复用性。

* 提高程序效率。库文件通常是经过高度优化和测试的，可以避免重复编写已有的功能，节省开发时间。

* 简化代码维护。将大工程划分为多个模块，每个模块作为独立的库，可以单独维护，只要库的交互头文件不改动，库内部的修改对其他部分是透明的。

* 模块化更新和跨平台开发。通过添加特定平台的库文件，可以实现跨平台的功能适配，保证程序在不同平台上的运行一致性。

* **保密作用**。库文件可以封装底层函数实体，提供给用户可执行代码的二进制形式，同时保护底层实现细节。

     代码混淆   yaosang--->yy    add-->a

* 加快项目开发。通过将大工程划分为多个模块，每个模块作为独立的库，可以加快开发速度，提高开发效率。

### 2、是什么

库文件一般就是编译好的二进制文件，用于在链接阶段同目标代码一起生成可执行文件，或者运行可执行文件的时候被加载，以便调用库文件中的某段代码。库文件无法直接执行，因为它的源代码中没有入口主函数，而只是一些函数模块的定义和实现，所以无法直接执行。库文件使程序更加模块化，重新编译更快，更新更容易。

### 3、分类

#### 3.1、静态库（类似于书店，只卖不借）

在编译程序的时候静态库的内容会被完成的复制到程序的内部，

* 优点
  当我们运行该程序是不会出现缺失的问题
* 缺点
  不利于功能的更新
  需要占用更多的内存

#### 3.2、动态库（类似于图书馆， 只借不买）

在编译的时候动态库并没被复制到程序中， 而是检查将否是否异常*（参数、返回值、函数名...）

* 优点
  相对于静态库来说占用更少的内存
  对程序执行的效率有一定的提升
* 缺点
  当程序执行的时候需要有动态库的支持

### 4、命名

* 使用lib作为前缀： 比如 libDeployPkg.so.0 / libhgfs.so.0 .....
* 静态库一般以 .a 为后缀 ， 动态库一般以.so为后缀
* 库文件会有不同的版本， 一般写在后缀后面， 比如 lib.a.so.0.1.2

  > 1 libc.so.1.0.3
  > 2 lib 库文件的前缀的名字（链接库文件时 ，只需要写上该名字）.so 后缀（so为动态库/共享库 a 则是静态库）
  > 5 .1 库文件的版本号
  > 6 .0.3 修正号

## 三、制作与使用

不管需要制作的是静态库还是动态库，原材料都是.o文件 （可重定位文件）
库文件内部不允许出现主函数main

> gcc -c xxx.c -o xxx.o

### 1、静态库的制作

#### 1.1、* 先获得.o文件

```shell
gcc -c src/add.c -o obj/add.o -I inc
gcc -c src/sub.c -o obj/sub.o -I inc
gcc -c src/main.c -o obj/main.o -I inc
```



#### 1.2、* 把以上生成的两个.o文件一起编译生成静态库文件

```shell
ar -rcs lib/libmymath.a obj/add.o obj/sub.o
```



#### 1.3、* 何使用静态库文件

```shell
gcc obj/main.o  -I inc -L lib -l mymath -o bin/mymath
```

![1710316877658](images\1710316877658.png)

### 2、动态库的制作

#### 2.1、先获得.o文件

> gcc -c src/add.c -o obj/add.o -I inc-fPIC
> gcc -c src/sub.c -o obj/sub.o -I inc-fPIC
> gcc -c src/main.c -o obj/main.o -I inc-fPIC

#### 2.2、把.o 文件制作成动态链接库

> gcc -shared -fPIC -o lib/libmymath.so obj/add.o obj/sub.o

#### 2.3、如何使用动态链接

编译

```shell
gcc obj/main.o -Wl,-rpath=. -I./inc -L lib -l mymath -o bin/mymath
```

查看rpath

```shell
readelf -d ./main
```



#### 2.4、运行

动态库如果未指定rpath则一定要指定环境变量路径

> export LD_LIBRARY_PATH=/home/lee/文档/cpp/code/lib:$LD_LIBRARY_PATH

## 四、静态库动态库相结合使用

```shell
gcc -c src/add.c -o obj/add.o -I inc
gcc -c src/sub.c -o obj/sub.o -I inc -fPIC
gcc -c src/main.c -o obj/main.o -I inc
ar -rcs lib/libadd.a obj/add.o
gcc -shared -fPIC -o lib/libsub.so obj/sub.o
gcc obj/main.o -I./inc -L lib -l sub  -o bin/mymath lib/libadd.a
```

## 五、CMake编译

```cmake
 
# 添加头文件搜索路径  
include_directories(inc)  

# 将add_obj编译为静态库  
add_library(add_static src/add.c)  
  
# 将sub_obj编译为动态库  
add_library(sub_shared SHARED src/sub.c)  
  
# 编译main.c，并链接到静态库和动态库  
add_executable(main src/main.c)  

target_link_libraries(main PRIVATE add_static sub_shared)  

SET_TARGET_PROPERTIES(main PROPERTIES BUILD_RPATH ".")
```





## 六、练习

1. 自行使用CMake制作并使用静态库文件。
2. 自行使用CMake制作并使用动态库文件。

