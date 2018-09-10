---
layout:       post
title:        "操作系统-引论"
subtitle:     "简单介绍操作系统相关的概念，主要介绍系统调用"
date:         2018-9-11 20:57:10
author:       "garaguru"
header-img:   "img/in-post/post-eleme-example/example.jpg"
header-mask:  0.3
catalog:      true
multilingual: true
tags:
    - 操作系统
    - 计算机科学
    - 系统调用
---

## 操作系统的功能
- 隐藏硬件，提供良好、清晰、优雅的抽象
- 管理计算机资源
    - 多路复用：时间上复用，空间上复用

## 操作系统的概念
- 进程：
    - 本质是正在执行的一个程序
    - 进程的地址空间（核心映像）：包括可执行程序、程序的数据、程序的堆栈
    - 进程表：数组或链表结构，保存进程被挂起时的所有信息
    - 可以创建一个或多个子进程
    - UID：每个进程都关联一个UID，这个UID是启动该进程的用户的UID，子进程的UID与父进程一样
    - PID：每个进程的唯一标识，PPID是该进程的父进程ID
- 文件：
    - mount系统调用允许把CD-ROM上的文件系统连接到程序所希望的 **根文件目录** 上
    - UNIX中的特殊文件：使IO设备看起来像文件一般
       - 块特殊文件：可随机存取的块组成的设备，如磁盘
       - 字符特殊文件：接收或输出字符流的设备，如打印机
    - 管道：一种虚文件，连接两个进程，使得两个进程之间的通信类似于普通文件的读写
- I/O：
    - 权限：rwx rwx rwx，可读 可写 可执行，所有者 组员 其他人
    - shell：不是操作系统的一部分
    - 虚拟内存：提供了运行比机器物理内存大的程序的能力，使得程序可以在运行时动态地链接库
- 系统调用(POSIX)：
    > The Portable Operating System Interface (POSIX)：A family of standards specified by the IEEE Computer Society for maintaining compatibility between operating systems.

    - 只有系统调用可以进入内核，过程调用不行
    - 系统调用过程（以 `read(fd,buffer,nbytes)` 为例）：
        1. push nbytes //读出的字节数
        2. push &buffers //缓冲区
        3. push fd //指定文件
        4. 调用read（库过程）
        5. 把用于read的代码放入寄存器中
        6. 从用户态切换到内核态（TRAP）
        7. 检查系统调用编号，分派给正确的系统调用处理器，这通常由一张系统调用编号所引用的、指向系统调用处理器的指针表来完成
        8. 系统调用处理器运行
        9. 返回库过程
        10. 返回用户程序
        11. 清除堆栈
    - 进程管理：
        - pid=fork()：创建与父进程相同的子进程，是POSIX中唯一可以创建进程的途径，返回的值在子进程中为0，在父进程中为PID
        - pid=waitpid(pid,&statloc,options)：父进程等待子进程终止（将第一个参数设置为-1，即等待所有子进程终止）（将第二个参数设置为&status，即子进程退出状态）
        - s=exec(name,argv,environp)：出错返回-1，替换一个子进程的核心映像
        - 进程的存储空间：FFFF-堆栈-空闲区-数据-正文-0000
    - 文件管理：open(),lseek(),read(),write(),close()
    - 目录管理：
        - mkdir()&rmdir()：创建和删除目录
        - link("usr/jim/memo","usr/ast/note")：允许同一个文件以多个名称出现（通常在不同目录下），用于共享同一个文件（本质是使用已有文件的inode创建另一个目录项，使用unlink()移除其中一个文件时，另一个不受影响，两个都被移除时，文件会被从磁盘中移除）

