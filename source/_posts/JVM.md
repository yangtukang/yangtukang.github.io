---
title: JVM
date: 2023-05-24 21:04:34
tags: [JVM]
---

# JVM简述

## 基本介绍

JVM：全称Java Virtual Machine，一个虚拟计算机，Java程序的运行环境

## 特点

*   JVM基于二进制字节码执行，由一套字节码指令集、一组寄存器、一个栈、一个垃圾回收堆、一个方法区等组成
*   JVM屏蔽了操作系统平台相关的信息，从而能够让Java程序只需要生成能够在JVM上运行的字节码文件，通过该机制实现跨平台性（一次编译，处处运行）
*   自动的内存管理，垃圾回收机制

## JVM结构

![JVM、JRE、JDK.drawio](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1202305242128858.png)

## 生命周期

>   JVM的生命周分为三个阶段：启动、运行、死亡

### 启动

当启动一个Java程序时，通过引导类加载器（boostrap class loader）创建一个初始类（initial class），对于拥有main函数的类就是JVM实例运行的起点

### 运行

main()方法是一个程序的初始起点，任何线程均在此启动

在JVM内部拥有两种线程类型，分别为：用户线程和守护线程，JVM使用的是**守护线程**，main()和其他线程使用的都是**用户线程**

执行一个Java程序时，真正执行的是一个**Java虚拟机的线程**

JVM有两种运行模式Server与Client，两种模式的区别在于：Client模式启动速度较快，Server模式启动方式较慢；但进入稳定期长期运行后Server模式运行速度比Client要快很多

Server模式启动的JVM采用的是重量级的虚拟机，对程序采用了更多的优化

Client模式启动的JVM采用的是轻量级的虚拟机

### 死亡

当程序中的用户线程都中止，JVM才会退出

程序正常执行结束、程序异常或者错误而异常终止、操作系统错误导致终止

线程调用Runtime类halt方法或System类exit方法，并且Java安全管理器允许这次exit或halt操作

Runtime.getRuntime().halt()：**强制终止正在运行的JVM**，此方法不会触发JVM关闭序列。当我们调用*halt*方法时，都不会执行关闭钩子或终结器。

System.exit()：**停止正在运行的Java虚拟机**。但是，在停止JVM之前，它将**调用关闭序列**，也称为有序关闭

## 内存结构

### JVM内存

>   内存结构是JVM中非常重要的一部分，是非常重要的系统资源，是硬盘和CPU的桥梁，承载着操作系统和应用程序的实例运行，又叫**运行时数据区**
>
>   JVM内存结构规定Java在运行过程中内存申请、分配、管理的策略，保证了JVM的高效稳定运行

![JAVA8内存结构图.drawio](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1202305242223649.png)

#### 程序计数器

>   程序计数器（寄存器）Program Counter Register 

#### 虚拟机栈

>   Java 虚拟机栈：Java Virtual Machine Stacks，每个线程运行时所需要的内存

#### 本地方法栈

>   本地方法栈：Native Method Stacks 为虚拟机执行本地方法(native方法)时提供服务的

#### 堆

>   堆：Heap，是JVM内存中最大的一块，由所有线程共享，由垃圾回收管理的主要区域，堆中对象大部分都要考虑线程安全的问题

##### StringTable/String Pool

>   字符串常量池(String Pool/StringTable/串池)存储的是String对象的直接引用或者对象，即保存着所有字符串字面量(literal strings)，这些字面量在编译器就确定，字符串变量池类似于Java系统级别提供的缓存，存放对象和引用

>   StringTable，类似于HashTable结构

###### 字符串拼接

常量池中的字符串仅是符号，第一次使用时才会变为对象（加入到运行时常量池），可以避免重复创建字符串对象

#### 方法区

### 本地内存

### JVM运行原理

## 类的加载过程

>   Java中数据类型分为**基本数据类型**和**引用数据类型**。基本数据类型由虚拟机预先定义，引用数据类型则需要进行类的加载

按照Java虚拟机规范，从class文件加载到内存中的类，到类卸载出内存为止，它的整个生命周期包含7个阶段

### 加载 （Loading）

### 连接（Linking）

#### 验证（Verification）

#### 准备（Preparation）

#### 解析（Resolution）

### 初始化（Initialization）

### 使用（Using）

### 卸载（Unloading）

![JVM类加载生命周期.drawio](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1202305242310912.png)
