---
layout: post
title: Inline Assembly 简介
excerpt: 本文介绍如何在 C/C++ 中嵌入汇编代码
date: 2019-09-06 21:51:43
categories: C/C++
tags: C/C++ 汇编
author: Garaguru
mathjax: true
---

* content
{:toc}

## 简介

本文将讲解inline assembly，那么什么是inline？inline简单来说就是告诉编译器，当一个inline函数被调用时，直接将这段函数的代码插入到他调用的地方，显然这种方式会减少函数调用的损耗。

而现在我们就很容易理解 inline assembly 是什么意思了，将汇编代码以一个 inline 函数的形式插入到我们的c/c++ 程序中。

通过关键字 **asm**，我们可以轻易地将汇编代码与c/c++代码混合起来

## gcc中汇编的语法

常见的汇编语言风格有两种：intel与AT&T，GCC使用AT&T语法

下面是intel与AT&T风格的一些重要区别：
- 寄存器命名：AT&T中寄存器通常有%前缀，比如%eax, %rbx 等，而intel风格中没有这个前缀，比如eax, rbx
- 操作数顺序：AT&T中，源操作数在前面，目标操作数在后面，比如intel风格中``mov eax, edx``，在AT&T风格里就是``mov %eax, %edx``
- 操作数大小：AT&T中，可以由操作名的后缀来指示操作数的size，比如``movl %edx, %eax``表示是一个(32-bit)long的操作数，同理,`b`表示(8-bit)byte,``w``表示(16-bit)word。
- 立即数：AT&T中，立即数用``$``前缀来表示
- 内存指示：不对操作数加前缀表示这是一个内存地址；比如``movl $bar, %ebx``表示把变量bar的地址直接存到%ebx中，但``movl bar, %ebx``就表示把bar地址对应的内容存到%ebx中了。
- 间接寻址：``movl 8(%ebp), %eax``，表示将括号内的地址偏移为8的地址的内容存到%eax中

## asm & \_\_asm__
```c
asm("assembly code");
/* or */
__asm__("assembly code");
```

Example：
```c
#include <stdio.h>

int main() {
    /* Add 10 and 20 and store result into register %eax */
    __asm__ ( "movl $10, %eax;"
                "movl $20, %ebx;"
                "addl %ebx, %eax;"
    );

    /* Subtract 20 from 10 and store result into register %eax */
    __asm__ ( "movl $10, %eax;"
                    "movl $20, %ebx;"
                    "subl %ebx, %eax;"
    );

    /* Multiply 10 and 20 and store result into register %eax */
    __asm__ ( "movl $10, %eax;"
                    "movl $20, %ebx;"
                    "imull %ebx, %eax;"
    );

    return 0 ;
}
```
## extended assembly
寄存器表
```
+---+--------------------+
| r |    Register(s)     |
+---+--------------------+
| a |   %eax, %ax, %al   |
| b |   %ebx, %bx, %bl   |
| c |   %ecx, %cx, %cl   |
| d |   %edx, %dx, %dl   |
| S |   %esi, %si        |
| D |   %edi, %di        |
+---+--------------------+
```
在扩展汇编中，我们可以指定操作数
```c
asm ( "assembly code"
           : output operands                  /* optional */
           : input operands                   /* optional */
           : list of clobbered registers      /* optional */
);

/* example */
int no = 100, val ;
    asm ("movl %1, %%ebx;"
         "movl %%ebx, %0;"
         : "=r" ( val )        /* output */
         : "r" ( no )         /* input */
         : "%ebx"         /* clobbered register */
     );
```
- 上面的例子中，%0指定val，%1指定no，"r"表示GCC可以用任意寄存器来存储操作数，输出操作数需要有"="，用来表示这个输出是只读的；"%%" 用来使GCC分辨一般操作数（"%"前缀）和寄存器
- clobbered register 是用来通知GCC，说这个寄存器的值会在asm里修改，所以GCC就不会用这个值来存储其他变量了。

```c
#include <stdio.h>
int main() {
    int arg1, arg2, add, sub, mul, quo, rem ;

    printf( "Enter two integer numbers : " );
    scanf( "%d%d", &arg1, &arg2 );

    /* Perform Addition, Subtraction, Multiplication & Division */
    __asm__ ( "addl %%ebx, %%eax;" : "=a" (add) : "a" (arg1) , "b" (arg2) );
    __asm__ ( "subl %%ebx, %%eax;" : "=a" (sub) : "a" (arg1) , "b" (arg2) );
    __asm__ ( "imull %%ebx, %%eax;" : "=a" (mul) : "a" (arg1) , "b" (arg2) );

    __asm__ ( "movl $0x0, %%edx;"
              "movl %2, %%eax;"
              "movl %3, %%ebx;"
              "idivl %%ebx;" : "=a" (quo), "=d" (rem) : "g" (arg1), "g" (arg2) );

    printf( "%d + %d = %d\n", arg1, arg2, add );
    printf( "%d - %d = %d\n", arg1, arg2, sub );
    printf( "%d * %d = %d\n", arg1, arg2, mul );
    printf( "%d / %d = %d\n", arg1, arg2, quo );
    printf( "%d %% %d = %d\n", arg1, arg2, rem );

    return 0 ;
}
```