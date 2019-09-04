---
layout: post
title: llvm intro
date: 2019-07-19 15:30:35.000000000 +08:00
tags: ios
---

翻译一下Chris Lattner写的介绍LLVM介绍的文章[LLVM](http://www.aosabook.org/en/llvm.html)

This chapter discusses some of the design decisions that shaped LLVM1, an umbrella project that hosts and develops a set of close-knit low-level toolchain components (e.g., assemblers, compilers, debuggers, etc.), which are designed to be compatible with existing tools typically used on Unix systems. The name "LLVM" was once an acronym, but is now just a brand for the umbrella project. While LLVM provides some unique capabilities, and is known for some of its great tools (e.g., the Clang compiler2, a C/C++/Objective-C compiler which provides a number of benefits over the GCC compiler), the main thing that sets LLVM apart from other compilers is its internal architecture.

```
本章讨论了形成LLVM的一些设计决策 // 这里的shaped的意思是形成/创建...，

一个雨伞(综合)型的项目，包含了一系列的，和底层紧密结果的工具，// knit:编织

这些工具被设计成和现有的通常在unix系统上用的的工具兼容 // compatible: 兼容；这里的typically 跟在tools后面，意思是这些tools通常用在unix系统

LLVM是首字母缩写，但是现在是这个项目的品牌 // acronym:首字母缩略词; LLVM（Low Level Virtual Machine）

虽然LLVM提供了一些独特的功能，并且以其一些强大的工具而闻名（例如，Clang compiler2，一个C / C ++ / Objective-C编译器，它提供了许多优于GCC编译器的优点），但是让LLVM区别于其他编译​​器的主要是，它的内部架构。// while: 虽然但是；capabilities：功能
```

From its beginning in December 2000, LLVM was designed as a set of reusable libraries with well-defined interfaces [LA04]. At the time, open source programming language implementations were designed as special-purpose tools which usually had monolithic executables. For example, it was very difficult to reuse the parser from a static compiler (e.g., GCC) for doing static analysis or refactoring. While scripting languages often provided a way to embed their runtime and interpreter into larger applications, this runtime was a single monolithic lump of code that was included or excluded. There was no way to reuse pieces, and very little sharing across language implementation projects.

```
从最开始，2000/12，LLVM被设计为一系列可重用的libs，这些libs有定义良好的接口

当时，开源编程语言实现是为了特殊目的工具，这些实现通常有巨大的可自行文件（复数）。// designed as：為某目的或用途而製造或計劃. This course is designed as an introduction to the subject. 這門課程是作為該科目的入門課而開設的。；这里”通常有巨大的可自行文件（复数）“，是指tools有，还是implementations？应该是指implementations

例如，很难重用静态编译器（例如，GCC）的解析器，进行静态分析或重构。

虽然脚本语言通常提供了一种将运行时和解释器嵌入大型应用程序的方法，但这个运行时是单个整体代码块被导出或导出。// monolithic: 巨大的，单一的，too large, too regular, or without interesting differences, and unwilling or unable to be changed，这里强调运行时是单一的，巨大的，不能分片引入

没有办法重复使用片段，并且很少在语言实现项目中共享。
```

Beyond the composition of the compiler itself, the communities surrounding popular language implementations were usually strongly polarized: an implementation usually provided either a traditional static compiler like GCC, Free Pascal, and FreeBASIC, or it provided a runtime compiler in the form of an interpreter or Just-In-Time (JIT) compiler. It was very uncommon to see language implementation that supported both, and if they did, there was usually very little sharing of code.

```
除了编译器本身的组成之外，// composition: 组成

流行语言实现的社区通常是强烈的两极化：// surrounding: 环绕; polarize: 两级化

一个实现通常提供传统的静态编译器，如GCC，Free Pascal和FreeBASIC，// either or: 两者取其一

或者它提供了一个解释器形式的运行时编译器或即时（JIT）编译器。// interpreter：解释

看到支持两者的语言实现非常罕见，如果他们这样做，通常很少共享代码。
```

Over the last ten years, LLVM has substantially altered this landscape. LLVM is now used as a common infrastructure to implement a broad variety of statically and runtime compiled languages (e.g., the family of languages supported by GCC, Java, .NET, Python, Ruby, Scheme, Haskell, D, as well as countless lesser known languages). It has also replaced a broad variety of special purpose compilers, such as the runtime specialization engine in Apple's OpenGL stack and the image processing library in Adobe's After Effects product. Finally LLVM has also been used to create a broad variety of new products, perhaps the best known of which is the OpenCL GPU programming language and runtime.

```
在过去的十年中，LLVM已经彻底改变了这种状况。// substantially: 大体上，可观的；alter：改变；landscape: 风景

LLVM现在被用作实现各种静态和运行时编译语言的通用基础结构（例如，由GCC，Java，.NET，Python，Ruby，Scheme，Haskell，D支持的语言系列，以及无数的较小的语言已知语言）。// infrastructure: 基础设施

它还取代了各种各样的专用编译器，例如Apple的OpenGL堆栈中的运行时专用引擎和Adobe After Effects产品中的图像处理库。

最后，LLVM还被用于创建各种各样的新产品，其中最着名的可能是OpenCL GPU编程语言和运行时。
```

