---
layout:     post
title:      "Operations on HCS12 Microcontroller"
subtitle:   " \"我的CMPEN472上课有感\""
date:       2016-02-28 12:00:00
author:     "Siwei"
header-img: "img/post-bg-2015.jpg"
tags:
    - Microcontroller
---

> “Yeah It's on. ”


## 前言
上了3年大学，终于赶上了一个有关我专业的课 ---- Microcontroller

就是这样一块简单的单片机，没想到居然折磨了我整整一个学期

[可以跳过感想](#build)

这节课的教授是一个韩国人，为人很好，就是感觉听不太懂我们的问题，每次去他的office hour问问题，我说了一大串，然后他来一句what's your problem

当然了，也存在我问的不清楚的地方，但是美国人问问题你也问他what's your problem就不太合适了吧

<p id = "build"></p>
---

## 正文

言归正传

这节课的码都是用汇编语言（assembly language）写的，很好玩。 最开始只是很简单的parallel port I/O, subroutine, looping and timing，程序也很短，按照要求让单片机上的 LED 亮起或者隔几秒亮一下。

一个月以后，基本的事情都掌握了，开始写复杂的了。

第六个作业，按照输入的数字，转化为百分比，使得LED按照此亮度亮起。由于机器只知道9是9，但是不知道95是95，所以要依次记录下每次输入的数字，判断到底输入了几个数字。 每输入一个数字，必须要把它存起来，因为这个单片机没有那么高端

如果是2位数，那么就是a * 10 + b得到最后的亮度，如果超过了100，还要提示这个数字超过了范围。 这个作业也不难。

第七个作业，写一个只能做3位数以内的加减乘除的计算器。 首先，我们要判断第一个数字的位数，然后还要判读符号，最后是第二个。 我的方法（复杂）是：分布建立3个初始值为0的变量储存前三个（f1,f2,f3)、后三个(s1,s2,s2)输入的数字，以及一个存储单位的变量--var。

把他们分别存起来，先在variable/data section建立一段可以储存20Byte的空间buff  DS.B  20。在code section，把储存器buff的地址存在X上：ldx   #buff，之后不断地staa  1,x+，这样没输入一个字符，就会存在x上，然后x++

写一个subroutine来计算每次输入的数的值，100*f3+10*f2+f1.由于寄存器A,B都是8位，所以存的数最大为255，所以用X,Y更安全。经过STX NUM1,STY NUM2以后，就可以进行计算了。

**真正的难点在于加减乘除，**因为存在进位的问题，如2+9，2-9就会产生carry/borrow bit，寄存器A对应前8位，B对应后8位，由AB组成的D就是16位，LDD NUM1后，对第一个数的A加1，然后再加NUM2，最后减掉#256就可以避免由carry/borrow产生的问题。

乘除法就简单的很多，利用EMUL以及IDIV就可以完成。

最后将结果存在RESULT中，print出来就可以了。


---

## 后记

暂时先写这么多，都是我临时想起来的，所以肯定有问题，而且不是很详细，

—— Siwei 后记于 2016.06
