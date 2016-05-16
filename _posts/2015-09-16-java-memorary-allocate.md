---
layout: post
title: 理解Java中的内存分配
tags: Java
category: 技术
date: 2015-09-16 16:16:39
---

![](http://7xlkoc.com1.z0.glb.clouddn.com/java0.jpg "茶馆、岛、慵懒的猫...都是美好的时光...")
<font color="red">**<center>本文部分内容转载于网络，站长已阅读并测试，保证其正确性。Update:2015-11-4</center>**</font>
照编译原理的观点,程序运行时的内存分配有三种策略,分别是静态的,栈式的,和堆式的.
 静态存储分配是指在编译时就能确定 每个数据目标在运行时刻的存储空间需求,因而在编译时就可以给他们分配固定的内存空间.这种分配策略要求程序代码中不允许有可变数据结构(比如可变数组) 的存在,也不允许有嵌套或者递归的结构出现,因为它们都会导致编译程序无法计算准确的存储空间需求.
 栈式存储分配也可称为动态存储分配,是 由一个类似于堆栈的运行栈来实现的.和静态存储分配相反,在栈式存储方案中,程序对数据区的需求在编译时是完全未知的,只有到运行的时候才能够知道,但是 规定在运行中进入一个程序模块时,必须知道该程序模块所需的数据区大小才能够为其分配内存.和我们在数据结构所熟知的栈一样,栈式存储分配按照先进后出的 原则进行分配。
 静态存储分配要求在编译时能知道所有变量的存储要求,栈式存储分配要求在过程的入口处必须知道所有的存储要求,而堆式存储分 配则专门负责在编译时或运行时模块入口处都无法确定存储要求的数据结构的内存分配,比如可变长度串和对象实例.堆由大片的可利用块或空闲块组成,堆中的内 存可以按照任意顺序分配和释放. 
** 堆和栈的比较**
 上面的定义从编译原理的教材中总结而来,除静态存储分配之外,都显得很呆板和难以理解,下面撇开静态存储分配,集中比较堆和栈:
 从堆和栈的功能和作用来通俗的比较,堆主要用来存放对象的，栈主要是用来执行程序的.而这种不同又主要是由于堆和栈的特点决定的:
 在编程中，例如C/C++中，所有的方法调用都是通过栈来进行的,所有的局部变量,形式参数都是从栈中分配内存空间的。实际上也不是什么分配,只是从栈顶 向上用就行,就好像工厂中的传送带(conveyor belt)一样,Stack Pointer会自动指引你到放东西的位置,你所要做的只是把东西放 下来就行.退出函数的时候，修改栈指针就可以把栈中的内容销毁.这样的模式速度最快,当然要用来运行程序了.需要注意的是,在分配的时候,比如为一个即将 要调用的程序模块分配数据区时,应事先知道这个数据区的大小,也就说是虽然分配是在程序运行时进行的,但是分配的大小多少是确定的,不变的,而这个"大小 多少"是在编译时确定的,不是在运行时.
 堆是应用程序在运行的时候请求操作系统分配给自己内存，由于从操作系统管理的内存分配,所以在分配 和销毁时都要占用时间，因此用堆的效率非常低.但是堆的优点在于,编译器不必知道要从堆里分配多少存储空间，也不必知道存储的数据要在堆里停留多长的时 间,因此,用堆保存数据时会得到更大的灵活性。事实上,面向对象的多态性,堆内存分配是必不可少的,因为多态变量所需的存储空间只有在运行时创建了对象之 后才能确定.在C++中，要求创建一个对象时，只需用new命令编制相关的代码即可。执行这些代码时，会在堆里自动进行数据的保存.当然，为达到这种灵活 性，必然会付出一定的代价:在堆里分配存储空间时会花掉更长的时间！这也正是导致我们刚才所说的效率低的原因,看来列宁同志说的好,人的优点往往也是人的 缺点,人的缺点往往也是人的优点(晕~). 

 **JVM中的堆和栈** 
 JVM是基于堆栈的虚拟机.JVM为每个新创建的线程都分配一个堆栈.也就是说,对于一个Java程序来说，它的运行就是通过对堆栈的操作来完成的。堆栈以帧为单位保存线程的状态。JVM对堆栈只进行两种操作:以帧为单位的压栈和出栈操作。
 我们知道,某个线程正在执行的方法称为此线程的当前方法.我们可能不知道,当前方法使用的帧称为当前帧。当线程激活一个Java方法,JVM就会在线程的 Java堆栈里新压入一个帧。这个帧自然成为了当前帧.在此方法执行期间,这个帧将用来保存参数,局部变量,中间计算过程和其他数据.这个帧在这里和编译 原理中的活动纪录的概念是差不多的.
 从Java的这种分配机制来看,堆栈又可以这样理解:堆栈(Stack)是操作系统在建立某个进程时或者线程(在支持多线程的操作系统中是线程)为这个线程建立的存储区域，该区域具有先进后出的特性。
 每一个Java应用都唯一对应一个JVM实例，每一个实例唯一对应一个堆。应用程序在运行中所创建的所有类实例或数组都放在这个堆中,并由应用所有的线程 共享.跟C/C++不同，Java中分配堆内存是自动初始化的。Java中所有对象的存储空间都是在堆中分配的，但是这个对象的引用却是在堆栈中分配,也 就是说在建立一个对象时从两个地方都分配内存，在堆中分配的内存实际建立这个对象，而在堆栈中分配的内存只是一个指向这个堆对象的指针(引用)而已。
** GC的思考**
 Java为什么慢?JVM的存在当然是一个原因,但有人说,在Java中,除了简单类型(int,char等)的数据结构,其它都是在堆中分配内存(所以说Java的一切都是对象)，这也是程序慢的原因之一。
 我的想法是(应该说代表TIJ的观点),如果没有Garbage Collector(GC),上面的说法就是成立的.堆不象栈是连续的空间,没有办法指 望堆本身的内存分配能够象堆栈一样拥有传送带般的速度,因为,谁会为你整理庞大的堆空间,让你几乎没有延迟的从堆中获取新的空间呢?
 这个时 候,GC站出来解决问题.我们都知道GC用来清除内存垃圾,为堆腾出空间供程序使用,但GC同时也担负了另外一个重要的任务,就是要让Java中堆的内存 分配和其他语言中堆栈的内存分配一样快,因为速度的问题几乎是众口一词的对Java的诟病.要达到这样的目的,就必须使堆的分配也能够做到象传送带一样, 不用自己操心去找空闲空间.这样,GC除了负责清除Garbage外,还要负责整理堆中的对象,把它们转移到一个远离Garbage的纯净空间中无间隔的 排列起来,就象堆栈中一样紧凑,这样Heap Pointer就可以方便的指向传送带的起始位置,或者说一个未使用的空间,为下一个需要分配内存的对象" 指引方向".因此可以这样说,垃圾收集影响了对象的创建速度,听起来很怪,对不对?
 那GC怎样在堆中找到所有存活的对象呢?前面说了,在建 立一个对象时，在堆中分配实际建立这个对象的内存,而在堆栈中分配一个指向这个堆对象的指针(引用),那么只要在堆栈(也有可能在静态存储区)找到这个引 用,就可以跟踪到所有存活的对象.找到之后,GC将它们从一个堆的块中移到另外一个堆的块中,并将它们一个挨一个的排列起来,就象我们上面说的那样,模拟 出了一个栈的结构,但又不是先进后出的分配,而是可以任意分配的,在速度可以保证的情况下,Isn't it great?
 但是，列宁同志 说了,人的优点往往也是人的缺点,人的缺点往往也是人的优点(再晕~~).GC()的运行要占用一个线程,这本身就是一个降低程序运行性能的缺陷,更何况 这个线程还要在堆中把内存翻来覆去的折腾.不仅如此,如上面所说,堆中存活的对象被搬移了位置,那么所有对这些对象的引用都要重新赋值.这些开销都会导致 性能的降低.
 此消彼长,GC()的优点带来的效益是否盖过了它的缺点导致的损失,我也没有太多的体会,Bruce Eckel 是Java的支持者，王婆卖瓜，话不能全信.个人总的感觉是,Java还是很慢,它的发展还需要时间.
 上面的体会是我看了TIJ.3rdEdition.Revision4.0中第四章之后得出的,内容和前面的有些不同.我没有看过侯捷的中文版本,但我觉得,在关键问题上,原版的TIJ的确更值得一读.所以和中文版配合起来学习是比较不错的选择.