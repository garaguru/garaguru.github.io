---
layout: post
title: .o .a .so 文件详解
excerpt: 本文讲解 g++ 中的静态链接库和动态链接库的使用方式
date: 2018-10-04 01:08:38
categories: 编译原理
tags: 编译原理 GCC C++
author: Garaguru
mathjax: true
---

* content
{:toc}


## .o 目标文件：
- 编译时，c++编译器是以 **cpp文件 + include 的头文件**为单位编译的，最后会生成这样一个 .o 文件，这个文件再用链接器去链接之后，就可以得到最终的程序了
- `gcc -c xxx.cpp` 编译源文件时可将其编译成*.o格式

## .a 静态链接库：
本质相当于多个 .o 目标文件结合在一起，方便用于静态链接

**优点:**
- 节省了编译时间
- 方便做兼容
- 开发者可以对源代码保密

**.a 文件的生成：**
  - `ar rcs libxxx.a f1.o f2.o` 含义：使用 archive 程序将 f1.o 和 f2.o 加入到 libxxx.a 文件中，如果 libxxx.a 不存在，则创建新的文件
- 静态链接库的使用：
  - 使用 gcc 生成可执行代码时使用 -l 即可指定库函数 `gcc -lxxx x.c`，对应 libxxx.a

**实例**
1. 三个文件：`hello.h hello.c main.c`
2. 生成 .o 文件：`gcc -c hello.c`
3. 创建 .a 静态库文件： `ar -cr libmyhello.a hello.o`
4. 使用的三种方式：
   - `gcc main.c -L. –lmyhello -o hello`
   - `gcc -o hello main.c libmyhello.a`
   - `gcc -c main.c && gcc -o hello main.o libmyhello.a`

## .so 共享库/动态链接库：
共享库可以在可执行程序运行的时候加载

**优点**：
- 可以实现进程之间的资源共享
- 可以让程序的升级变得简单

**名字**
- soname 必须的格式：lib+共享库名+.so+版本号信息(但是非常底层的c库函数都不是以lib开头命名的)
  - e.g. `/usr/lib/libreadline.so.3`
- real name 真名：包含主版本号和发型版本号
- 编译时指定的名字：不包含版本号信息的 soname

**位置**
- GNU 标准建议所有的函数库放在 `/usr/local/lib` 目录下，建议命令和可执行文件放在 `/usr/local/bin` 目录下，具体我们可以查看或修改 `/etc/ld.so.conf` 里的配置信息

**创建**
- 先创建 .o 文件
- 将目标文件 通过 `gcc -fPIC` 加入共享库
- e.g. 
  - `gcc test_a.c test_b.c test_c.c -fPIC -shared -o libtest.so`
  - `gcc -shared -Wl,-soname,your_soname -o library_name file_list library_list`

**实例**
1. 三个文件：`hello.h hello.c main.c`
2. 生成 .o 文件：`gcc -c hello.c`
3. 创建共享库文件：`gcc -shared -fPIC -o libmyhello.so hello.o`
4. 使用：
   - `gcc -o hello main.c -L. -lmyhello`
   - `mv libmyhello.so /usr/lib`

## 部分编译参数解释
- `-shared` 该选项指定生成动态连接库，不用该标志外部程序无法连接
- `-fPIC`  作用于编译阶段，告诉编译器产生与位置无关代码(Position-Independent Code) 不用此选项的话编译后的代码是位置相关的，所以动态载入时是通过代码拷贝的方式来满足不同进程的需要，而不能达到真正代码段共享的目的
- `-L.` 表示要连接的库在当前目录中；
- -lmyhello 编译器查找动态连接库时有隐含的命名规则，即在给出的名字前面加上lib，后面加上.so或.a来确定库的名称libmyhello.so或libmyhello.a。
- `-I` 用于指定头文件所在的目录

## 搜索路径的顺序
**静态库**
1. ld(GNU linker) 会去找GCC命令中的参数 `-L`
2. 再找gcc的环境变量 `LIBRARY_PATH`
3. 再找内定目录 `/lib /usr/lib /usr/local/lib` 这是当初compile gcc时写在程序内的

**动态库**
1. 编译目标代码时指定的动态库搜索路径
2. 环境变量 `LD_LIBRARY_PATH` 指定的动态库搜索路径
3. 配置文件 `/etc/ld.so.conf` 中指定的动态库搜索路径
4. 默认的动态库搜索路径 `/lib`
5. 默认的动态库搜索路径 `/usr/lib`

**LIBRARY_PATH** 环境变量：指定程序静态链接库文件搜索路径
**LD_LIBRARY_PATH** 环境变量：指定程序动态链接库文件搜索路径

**找到动态库的3种方式**：
- 把库拷贝到/usr/lib和/lib目录下。
- 在 LD_LIBRARY_PATH 环境变量中加上库所在路径。
    - 例如动态库 `libhello.so` 在 `/home/example/lib` 目录下：
    - `export LD_LIBRARY_PATH=LD_LIBRARY_PATH:/home/example/lib`

- 修改 `/etc/ld.so.conf` 文件，把库所在的路径加到文件末尾，并执行 `ldconfig` 刷新。这样，加入的目录下的所有库文件都可见。