---
layout: post
title: Concurrency Programming Guide
date: 2019-01-22 15:30:35.000000000 +08:00
tags: ios
---

翻译这篇官方文档
[Concurrency Programming Guide](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091-CH1-SW1)

## Introduction

Concurrency is the notion of multiple things happening at the same time. With the proliferation of multicore CPUs and the realization that the number of cores in each processor will only increase, software developers need new ways to take advantage of them. Although operating systems like OS X and iOS are capable of running multiple programs in parallel, most of those programs run in the background and perform tasks that require little continuous processor time. It is the current foreground application that both captures the user’s attention and keeps the computer busy. If an application has a lot of work to do but keeps only a fraction of the available cores occupied, those extra processing resources are wasted.

并发性是多个事物同时发生的概念。随着多核CPU的激增以及每个处理器中核心数量的增加，软件开发人员需要新的方法来利用它们。虽然OS X和iOS等操作系统能够并行运行多个程序，但大多数程序在后台运行并执行需要很少连续处理器时间的任务。它是当前的前台应用程序，既捕获用户的注意力，又使计算机保持忙碌状态。如果一个应用程序有很多工作要做，但只保留了一小部分可用内核，那么这些额外的处理资源就会被浪费掉。

In the past, introducing concurrency to an application required the creation of one or more additional threads. Unfortunately, writing threaded code is challenging. Threads are a low-level tool that must be managed manually. Given that the optimal number of threads for an application can change dynamically based on the current system load and the underlying hardware, implementing a correct threading solution becomes extremely difficult, if not impossible to achieve. In addition, the synchronization mechanisms typically used with threads add complexity and risk to software designs without any guarantees of improved performance.

过去，将并发引入应用程序需要创建一个或多个其他线程。不幸的是，编写线程代码很有挑战性 线程是一种必须手动管理的底层工具。鉴于应用程序的最佳线程数可以根据当前系统负载和底层硬件动态变化，实现正确的线程解决方案变得非常困难，即使不是不可能实现。此外，通常与线程一起使用的同步机制增加了软件设计的复杂性和风险，而没有任何改进性能的保证。

Both OS X and iOS adopt a more asynchronous approach to the execution of concurrent tasks than is traditionally found in thread-based systems and applications. Rather than creating threads directly, applications need only define specific tasks and then let the system perform them. By letting the system manage the threads, applications gain a level of scalability not possible with raw threads. Application developers also gain a simpler and more efficient programming model.

与传统的基于线程的系统和应用程序相比，OS X和iOS都采用了更加异步的并发任务执行方法。应用程序只需定义特定任务，然后让系统执行它们，而不是直接创建线程。通过让系统管理线程，应用程序获得了原始线程无法实现的可伸缩性级别。应用程序开发人员还可以获得更简单，更高效的编程模型。

This document describes the technique and technologies you should be using to implement concurrency in your applications. The technologies described in this document are available in both OS X and iOS.

### Organization of This Document

This document contains the following chapters:

- **Concurrency and Application Design** introduces the basics of asynchronous application design and the technologies for performing your custom tasks asynchronously.
- **Operation Queues** shows you how to encapsulate and perform tasks using Objective-C objects.
- **Dispatch Queues** shows you how to execute tasks concurrently in C-based applications.
- **Dispatch Sources** shows you how to handle system events asynchronously.
- **Migrating Away from Threads** provides tips and techniques for migrating your existing thread-based code over to use newer technologies.
This document also includes a glossary that defines relevant terms.

本文档包含以下章节：

- **并发和应用程序设计**介绍了异步应用程序设计的基础知识以及异步执行自定义任务的技术。
- **Operation Queues**向您展示如何使用Objective-C对象封装和执行任务。
- **Dispatch Queues**向您展示如何在基于C的应用程序中同时执行任务。
- **Dispatch Sources**向您展示了如何异步处理系统事件。
- **迁移远离线程**提供了有关迁移现有基于线程的代码以使用新技术的提示和技巧。
本文档还包括定义相关术语的词汇表。

### A Note About Terminology

Before entering into a discussion about concurrency, it is necessary to define some relevant terminology to prevent confusion. Developers who are more familiar with UNIX systems or older OS X technologies may find the terms “task”, “process”, and “thread” used somewhat differently in this document. This document uses these terms in the following way:

- The term thread is used to refer to a separate path of execution for code. The underlying implementation for threads in OS X is based on the POSIX threads API.
- The term process is used to refer to a running executable, which can encompass multiple threads.
- The term task is used to refer to the abstract concept of work that needs to be performed.

For complete definitions of these and other key terms used by this document, see Glossary.

关于术语的注释
在进行关于并发性的讨论之前，有必要定义一些相关的术语以防止混淆。更熟悉UNIX系统或旧OS X技术的开发人员可能会发现本文档中使用的术语“任务”，“进程”和“线程”略有不同。本文档以下列方式使用这些术语：

- 术语线程用于指代代码的单独执行路径。OS X中线程的底层实现基于POSIX线程API。
- 术语进程用于指代正在运行的可执行文件，其可以包含多个线程。
- 术语任务用于指代需要执行的抽象工作概念。

有关本文档使用的这些和其他关键术语的完整定义，请参阅术语表。

### See Also
This document focuses on the preferred technologies for implementing concurrency in your applications and does not cover the use of threads. If you need information about using threads and other thread-related technologies, see Threading Programming Guide.


## Concurrency and Application Design

并发和应用程序设计

In the early days of computing, the maximum amount of work per unit of time that a computer could perform was determined by the clock speed of the CPU. But as technology advanced and processor designs became more compact, heat and other physical constraints started to limit the maximum clock speeds of processors. And so, chip manufacturers looked for other ways to increase the total performance of their chips. The solution they settled on was increasing the number of processor cores on each chip. By increasing the number of cores, a single chip could execute more instructions per second without increasing the CPU speed or changing the chip size or thermal characteristics. The only problem was how to take advantage of the extra cores.

在计算的早期阶段，计算机可以执行的每单位时间的最大工作量取决于CPU的时钟速度。但随着技术的进步和处理器设计变得更加紧凑，热量和其他物理限制开始限制处理器的最大时钟速度。因此，芯片制造商寻找其他方法来提高芯片的总体性能。他们解决的解决方案是增加每个芯片上的处理器内核数量。通过增加内核数量，单个芯片可以每秒执行更多指令，而不会增加CPU速度或改变芯片尺寸或热特性。唯一的问题是如何利用额外的核心。

In order to take advantage of multiple cores, a computer needs software that can do multiple things simultaneously. For a modern, multitasking operating system like OS X or iOS, there can be a hundred or more programs running at any given time, so scheduling each program on a different core should be possible. However, most of these programs are either system daemons or background applications that consume very little real processing time. Instead, what is really needed is a way for individual applications to make use of the extra cores more effectively.

为了利用多个核心，计算机需要能够同时执行多项操作的软件。对于像OS X或iOS这样的现代多任务操作系统，在任何给定时间都可以运行一百个或更多程序，因此应该可以在不同的核心上安排每个程序。但是，这些程序中的大多数都是系统守护程序或后台应用程序，它们消耗的实际处理时间非常短。相反，真正需要的是各个应用程序更有效地利用额外内核的方法。

The traditional way for an application to use multiple cores is to create multiple threads. However, as the number of cores increases, there are problems with threaded solutions. The biggest problem is that threaded code does not scale very well to arbitrary numbers of cores. You cannot create as many threads as there are cores and expect a program to run well. What you would need to know is the number of cores that can be used efficiently, which is a challenging thing for an application to compute on its own. Even if you manage to get the numbers correct, there is still the challenge of programming for so many threads, of making them run efficiently, and of keeping them from interfering with one another.

应用程序使用多个核心的传统方式是创建多个线程。但是，随着核心数量的增加，线程解决方案也存在问题。最大的问题是线程代码不能很好地扩展到任意数量的内核。您不能创建与核心一样多的线程，并期望程序运行良好。您需要知道的是可以有效使用的核心数量，这对于应用程序自行计算来说是一项具有挑战性的事情。即使你设法得到正确的数字，仍然存在为这么多线程编程，使它们有效运行以及防止它们彼此干扰的挑战。

So, to summarize the problem, there needs to be a way for applications to take advantage of a variable number of computer cores. The amount of work performed by a single application also needs to be able to scale dynamically to accommodate changing system conditions. And the solution has to be simple enough so as to not increase the amount of work needed to take advantage of those cores. The good news is that Apple’s operating systems provide the solution to all of these problems, and this chapter takes a look at the technologies that comprise this solution and the design tweaks you can make to your code to take advantage of them.

因此，总结一下这个问题，应用程序需要有一种方法来利用可变数量的计算机内核。单个应用程序执行的工作量还需要能够动态扩展以适应不断变化的系统条件。解决方案必须足够简单，以免增加利用这些核心所需的工作量。好消息是Apple的操作系统为所有这些问题提供了解决方案，本章将介绍构成此解决方案的技术以及您可以对代码进行设计调整以利用它们。

### The Move Away from Threads

Although threads have been around for many years and continue to have their uses, they do not solve the general problem of executing multiple tasks in a scalable way. With threads, the burden of creating a scalable solution rests squarely on the shoulders of you, the developer. You have to decide how many threads to create and adjust that number dynamically as system conditions change. Another problem is that your application assumes most of the costs associated with creating and maintaining any threads it uses.

尽管线程已存在多年并且仍在继续使用，但它们并未解决以可伸缩方式执行多个任务的一般问题。使用线程，创建可扩展解决方案的负担完全取决于开发人员。您必须决定创建多少线程并在系统条件发生变化时动态调整该数量。另一个问题是您的应用程序承担与创建和维护它使用的任何线程相关的大部分成本。

Instead of relying on threads, OS X and iOS take an asynchronous design approach to solving the concurrency problem. Asynchronous functions have been present in operating systems for many years and are often used to initiate tasks that might take a long time, such as reading data from the disk. When called, an asynchronous function does some work behind the scenes to start a task running but returns before that task might actually be complete. Typically, this work involves acquiring a background thread, starting the desired task on that thread, and then sending a notification to the caller (usually through a callback function) when the task is done. In the past, if an asynchronous function did not exist for what you want to do, you would have to write your own asynchronous function and create your own threads. But now, OS X and iOS provide technologies to allow you to perform any task asynchronously without having to manage the threads yourself.

OS X和iOS采用异步设计方法，而不是依赖线程解决并发问题。多年来，异步功能已存在于操作系统中，并且通常用于启动可能需要很长时间的任务，例如从磁盘读取数据。调用时，异步函数会在后台执行一些操作以启动任务运行，但在该任务可能实际完成之前返回。通常，此工作涉及获取后台线程，在该线程上启动所需任务，然后在任务完成时向调用方发送通知（通常通过回调函数）。过去，如果你想要做的事情不存在异步函数，你必须编写自己的异步函数并创建自己的线程。但现在，OS X和iOS允许你去执行任何异步任务，不需要自己管理线程。

One of the technologies for starting tasks asynchronously is Grand Central Dispatch (GCD). This technology takes the thread management code you would normally write in your own applications and moves that code down to the system level. All you have to do is define the tasks you want to execute and add them to an appropriate dispatch queue. GCD takes care of creating the needed threads and of scheduling your tasks to run on those threads. Because the thread management is now part of the system, GCD provides a holistic approach to task management and execution, providing better efficiency than traditional threads.

异步启动任务的技术之一是Grand Central Dispatch（GCD）。此技术采用您通常在自己的应用程序中编写的线程管理代码，并将该代码下移到系统级别。您所要做的就是定义要执行的任务并将它们添加到适当的调度队列中。GCD负责创建所需的线程并安排在这些线程上运行的任务。由于线程管理现在是系统的一部分，因此GCD为任务管理和执行提供了一种整体方法，提供了比传统线程更高的效率。

Operation queues are Objective-C objects that act very much like dispatch queues. You define the tasks you want to execute and then add them to an operation queue, which handles the scheduling and execution of those tasks. Like GCD, operation queues handle all of the thread management for you, ensuring that tasks are executed as quickly and as efficiently as possible on the system.

操作队列是Objective-C对象，其行为与调度队列非常相似。您可以定义要执行的任务，然后将它们添加到操作队列，该队列处理这些任务的计划和执行。与GCD一样，操作队列为您处理所有线程管理，确保在系统上尽可能快速有效地执行任务。

The following sections provide more information about dispatch queues, operation queues, and some other related asynchronous technologies you can use in your applications.

### Dispatch Queues

Dispatch queues are a C-based mechanism for executing custom tasks. A dispatch queue executes tasks either serially or concurrently but always in a first-in, first-out order. (In other words, a dispatch queue always dequeues and starts tasks in the same order in which they were added to the queue.) A serial dispatch queue runs only one task at a time, waiting until that task is complete before dequeuing and starting a new one. By contrast, a concurrent dispatch queue starts as many tasks as it can without waiting for already started tasks to finish.

Dispatch queues have other benefits:

They provide a straightforward and simple programming interface.
They offer automatic and holistic thread pool management.
They provide the speed of tuned assembly.
They are much more memory efficient (because thread stacks do not linger in application memory).
They do not trap to the kernel under load.
The asynchronous dispatching of tasks to a dispatch queue cannot deadlock the queue.
They scale gracefully under contention.
Serial dispatch queues offer a more efficient alternative to locks and other synchronization primitives.

The tasks you submit to a dispatch queue must be encapsulated inside either a function or a block object. Block objects are a C language feature introduced in OS X v10.6 and iOS 4.0 that are similar to function pointers conceptually, but have some additional benefits. Instead of defining blocks in their own lexical scope, you typically define blocks inside another function or method so that they can access other variables from that function or method. Blocks can also be moved out of their original scope and copied onto the heap, which is what happens when you submit them to a dispatch queue. All of these semantics make it possible to implement very dynamic tasks with relatively little code.

Dispatch queues are part of the Grand Central Dispatch technology and are part of the C runtime. For more information about using dispatch queues in your applications, see Dispatch Queues. For more information about blocks and their benefits, see Blocks Programming Topics.

Dispatch队列是一种用于执行自定义任务的基于C的机制。一个调度队列顺序或同时却始终执行一个先入先出的顺序任务。（换句话说，调度队列始终以与添加到队列相同的顺序出列并启动任务。）串行调度队列一次只运行一个任务，等待该任务完成后再出列并启动新的一个。相比之下，并发调度队列可以启动尽可能多的任务，而无需等待已经启动的任务完成。

派遣队列还有其他好处：

它们提供简单易用的编程接口。
它们提供自动和整体的线程池管理。
它们提供了调整组装的速度。
它们的内存效率更高（因为线程堆栈不会在应用程序内存中停留）。
它们不会在负载下陷入内核。
将任务异步分派到调度队列不会使队列死锁。
它们在竞争中优雅地扩展。
串行调度队列为锁和其他同步原语提供了更有效的替代方案。
您提交给调度队列的任务必须封装在函数或块对象中。块对象是OS X v10.6和iOS 4.0中引入的C语言特性，在概念上类似于函数指针，但具有一些额外的好处。您通常在另一个函数或方法中定义块，而不是在自己的词法范围中定义块，以便它们可以从该函数或方法访问其他变量。块也可以移出其原始范围并复制到堆上，这是将它们提交到调度队列时发生的情况。所有这些语义都可以用相对较少的代码实现非常动态的任务。

Dispatch队列是Grand Central Dispatch技术的一部分，是C运行时的一部分。有关在应用程序中使用调度队列的更多信息，请参阅调度队列。有关块及其好处的更多信息，请参阅块编程主题。

### Dispatch Sources
