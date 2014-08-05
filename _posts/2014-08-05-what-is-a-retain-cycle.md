---
layout: post
title: "什么是引用循环（retain cycle）？"
description: ""
category: iOS
tags: [retain cycle, dealloc, memory leak]
---
{% include JB/setup %}

引用循环（retain cycle）会发生在像Objective-C这样的基于引用计数（retain count）内存管理技术的语言中。当对象被创建时，其引用计数为1。当其它对象引用它时，引用计数增加；当其它对象释放它时，引用计数减少。当对象的引用计数为0时，运行时（runtime）会将其释放（deallocate），这时对象的dealloc方法被调用。

假设有两个对象A和B，A创建并引用B。创建A的对象会负责释放A，假设A的dealloc方法会release B，当B的引用计数为0时，B被释放。为简单起见，假设没有其它额外的对象引用A或B。

但如果B需要引用A，增加A的引用计数时会怎样？创建A的对象会释放A，但是B现在也引用A，A的引用计数不为0。另一方面，A引用B，因此B的引用计数也不为0，A和B都不会被释放。B的dealloc方法不会被调用，因此计算B在其dealloc方法调用A的release方法也没有作用。

在此情况下，就发生了内存泄漏（memory leak）：没有其它对A或B的引用，但A和B都没有被释放。如果A和B正在做任何消耗cpu的东西，应用也会发生leaking CPU time。

reference: 

[What is a retain cycle?](http://www.quora.com/What-is-a-retain-cycle)

