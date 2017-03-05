---
title: Raspberry Pi GPIO
tags:
  - GPIO
id: 86
categories:
  - Operating System
date: 2014-12-24 12:00:04
---

树莓派的共有54个GPIO 这54个GPIO通过 0x7e20 0000 - 0x7e20 00b0 这41 个寄存器控制 。有用的寄存器有：

1.GPFSEL ( GPIO Function Select)    共六个GPFSEL0 - GPFSEL6，每个GPFSEL控制10个GPIO pin ，即GPFSEL的0-29 bit 每3bit控制一个GPIO pin 30、31两个bit无用。作用是控制GPIO pin 的功能。 功能号：

<!--more-->

000 = GPIO Pin  is an input
001 = GPIO Pin  is an output
100 = GPIO Pin  takes alternate function 0
101 = GPIO Pin  takes alternate function 1
110 = GPIO Pin  takes alternate function 2
111 = GPIO Pin  takes alternate function 3
011 = GPIO Pin  takes alternate function 4
010 = GPIO Pin takes alternate function 5

&nbsp;

2\. GPSET (GPIO Pin Output Set)     GPSET共两个GPSET0、GPSET1。

3.GPCLR (GPIO Pin Output Clear Registers)     GPCLR0、GPCLR1

4.GPLEV (GPIO Pin Level Registers)    GPLEV0、GPLEV1  给出相应GPIO Pin的值。

5.GPEDS (GPIO Event Detect Status Registers)

6.GPREN( GPIO Rising Edge Detect Enable Registers)

7.GPFEN(GPIO Falling Edge Detect Enable Registers)

8.GPHEN(GPIO High Detect Enable Registers )

9.GPLEN( GPIO Low Detect Enable Registers)

10.GPAREN(GPIO Asynchronous rising Edge Detect Enable Registers)

11.GPAFEN(GPIO Asynchronous Falling Edge Detect Enable Registers)

12.GPPUD(GPIO Pull-up/down Register)

13.GPPUDCLK(GPIO Pull-up/down Clock Registers)