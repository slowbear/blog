---
title: 操作系统学习笔记
author: Slowy
date: 2021-12-01
update: 2021-12-01
#description:
#image: 
categories: ebook
tags:
  - os
  - rust
---

> ~~未完待续~~

# C语言回顾

与Java不同，在C语言中，volatile的语义仅仅为“禁止编译器优化”，意味着volatile既不会引入任何memory barrier(也意味着不保证可见性)，也不保证任何原子性。

实际上，volatile对C语言的多线程编程完全没有帮助。尽管volatile禁止编译器优化，但实际上它也不完全能阻止指令重排。

譬如下面这段程序：

```c
static int y = 0;
volatile int x = 0;

void change() {
    ++y;
    ++x;
    ++y;
}
```

在gcc开启O2优化编译的x86汇编代码如下：

```asm
change:
  movl x(%rip), %eax // %eax = x
  addl $2, y(%rip)   // y += 2
  addl $1, %eax      // %eax += 1
  movl %eax, x(%rip) // x = %eax
  ret
x:
  .zero 4
```

可以看出，尽管x使用了volatile修饰符，但gcc还是可以重排指令，将两个`y++`合并成`y+=2`。

事实上volatile修饰符的作用非常有限，一般只用在：
- MMIO/PMIO的变量
- setjmp和longjmp使用的变量
- 处理信号时与sig_atomic_t配合使用

> 注：在Windows平台上，vc对volatile的处理与iso c中的volatile并不一致，详见https://learn.microsoft.com/zh-cn/cpp/cpp/volatile-cpp?view=msvc-170
