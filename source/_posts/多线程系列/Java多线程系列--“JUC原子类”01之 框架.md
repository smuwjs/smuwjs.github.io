---
title: "Java多线程系列--“JUC原子类”01之 框架"
categories: 
- source analysis
tags: 
- Java多线程系列
- Java源码分析
- JUC集合
date: 2016-11-13 01:00:00
---

根据修改的数据类型，可以将JUC包中的原子操作类可以分为4类。

1. **基本类型**: AtomicInteger, AtomicLong, AtomicBoolean ;  
2. **数组类型**: AtomicIntegerArray, AtomicLongArray, AtomicReferenceArray ;  
3. **引用类型**: AtomicReference, AtomicStampedRerence, AtomicMarkableReference ;  
4. **对象的属性修改类型**: AtomicIntegerFieldUpdater, AtomicLongFieldUpdater, AtomicReferenceFieldUpdater 。

这些类存在的目的是对相应的数据进行原子操作。所谓原子操作，是指操作过程不会被中断，保证数据操作是以原子方式进行的
