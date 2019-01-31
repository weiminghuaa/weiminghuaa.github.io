---
layout: post
title: Core graphics framework
date: 2019-01-04 15:30:35.000000000 +08:00
tags: ios
---

YY招聘指明需要懂的库

> 欢聚时代（YY Inc）研发部门招聘
  IOS高级开发工程师 
  17k-34k /广州 / 经验3-5年 / 本科及以上 / 全职


工作职责：
负责IOS平台的软件产品的开发,对整个单独app项目负责。
岗位要求：
1. 熟练使用Objective C语言，具备主流开源组件使用经验； 
2. 熟悉iPhone平台的开发特点和网络通信模型
3. **熟悉Xcode， UIKit，Cocoa Touch ； 熟悉Core Data, Core graphics framework ；**
4. 精通iOS下的多线程、网络、内存管理、UI开发；
5. 已在App Store发布过作品者优先。
6. 本科以上学历，4年以上移动平台开发经验
7. 思路清晰，善于思考，能独立分析和解决问题；责任心强，具备良好的团队合作精神和承受压力的能力
8. 熟悉软件工程，具有良好的代码编写规范和书写文档的习惯
9. 有很强的自学能力，喜欢钻研技术

## Quartz 2D

[Quartz 2D Programming Guide](https://developer.apple.com/library/archive/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007533-SW1)

sample code: Quartz2DforiOS

Core Graphics, also known as Quartz 2D, is an advanced, two-dimensional drawing engine available for iOS, tvOS and macOS application development. Quartz 2D provides low-level, lightweight 2D rendering with unmatched output fidelity regardless of display or printing device. Quartz 2D is resolution- and device-independent.

Core Graphics，也称为Quartz 2D，是一种先进的二维绘图引擎，可用于iOS，tvOS和macOS应用程序开发。 Quartz 2D提供底层，轻量级2D渲染，无论显示器或打印设备如何，都具有无与伦比的输出保真度。 Quartz 2D与分辨率和设备无关。

The Quartz 2D API is easy to use and provides access to powerful features such as transparency layers, path-based drawing, offscreen rendering, advanced color management, anti-aliased rendering, and PDF document creation, display, and parsing.

Quartz 2D API易于使用，并提供对强大功能的访问，如透明层，基于路径的绘图，屏幕外渲染，高级颜色管理，消除锯齿渲染以及PDF文档创建，显示和解析。

## 概况

Harness the power of Quartz technology to perform lightweight 2D rendering with high-fidelity output. Handle path-based drawing, antialiased rendering, gradients, images, color management, PDF documents, and more.
利用强大的Quartz技术，去执行轻量级2D渲染，渲染以高保真输出。处理基于路径的绘图，抗锯齿渲染，渐变，图像，颜色管理，PDF文档等。

The Core Graphics framework is based on the Quartz advanced drawing engine. It provides low-level, lightweight 2D rendering with unmatched output fidelity. You use this framework to handle path-based drawing, transformations, color management, offscreen rendering, patterns, gradients and shadings, image data management, image creation, and image masking, as well as PDF document creation, display, and parsing.

Core Graphics框架基于Quartz高级绘图引擎。 它提供底层，轻量级2D渲染，具有无与伦比的输出保真度。 您可以使用此框架来处理基于路径的绘制，转换，颜色管理，屏幕外渲染，图案，渐变和阴影，图像数据管理，图像创建和图像蒙版，以及PDF文档创建，显示和解析。

In macOS, Core Graphics also includes services for working with display hardware, low-level user input events, and the windowing system.

在macOS中，Core Graphics还包括这些服务：用于处理显示硬件，底层用户输入事件和窗口系统。

## 参考

[Practical Drawing for iOS Developers](https://developer.apple.com/videos/play/wwdc2011/129/)

[Core Graphics Framework](https://www.jianshu.com/p/158409a8eb86)

[iOS绘图教程](http://www.cnblogs.com/xdream86/archive/2012/12/12/2814552.html)

[iOS Core Graphics Framework绘图教程](https://www.jianshu.com/p/70c828ceab1d)

[Core Graphics的简介与概述（一）](https://blog.csdn.net/die_word/article/details/82460029)

[coreGraphics框架](https://blog.csdn.net/super_man_ww/article/details/51217484)

[Core Graphics框架 : 一个让程序猿成为视觉设计师的框架](https://www.jianshu.com/p/7f71c8f37ce9)