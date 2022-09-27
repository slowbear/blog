---
title: 异步编程随想
author: Slowy
date: 2022-11-22
update: 2022-11-22
#description:
#image: 
categories: programming
tags:
  - async
  - suspend
  - callback
---

> Async is hard.

在JavaScript里实现一个延迟任务非常简单：

```javascript
setTimeout(() => console.log("log after 2s"), 2000);
console.log("log right now");
```

这是一个非常典型的异步编程的例子：上述代码会先输出`log right now`，等待2s之后才会输出`log after 2s`。尽管这两行代码看起来很简单，但要理解它的工作原理名，至少需要了解以下几个概念：

- 事件循环
- 阻塞与非阻塞调用
- 定时器
- 并发（区别于并行）

本文将从多个编程语言出发，去理解和比较一般主流的异步编程模型。

# 什么是异步？

说到异步，大家往往把它和阻塞和非阻塞IO联系到一起，还有一个常被提及的概念是并发和并行。实际上这是三个不同维度上的东西，它们直接关系也并非完全正交，举几个例子：

- 一般Linux上的异步网络库都是用的`epoll`这组API实现的，但`epoll`的API却是阻塞的。
- 在浏览器中JavaScript代码一般是单线程执行的，这意味着不存在并行，但是却有并发和异步。
- 数据库可以被并行访问，但事务可能被阻塞。

为方便解释到底什么是异步，我需要引入有限状态机。

# 回调是一切的基础

# 如何理解 Promise

# 状态机, Generator 和 async/await

# 关于 Kotlin 的 Suspend

# Goroutine 与 Project Loom

# 总结
