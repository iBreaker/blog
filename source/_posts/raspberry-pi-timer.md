---
title: Raspberry Pi Timer
tags:
  - Raspberry Pi
  - Timer
  - 时钟
  - 树莓派
  - 频率
id: 88
categories:
  - Operating System
date: 2014-12-25 18:07:33
---

The Physical (hardware) base address for the system timers is 0x7E003000.

树莓派的Timer共有7个寄存器 分别是

0x0   CS       System Timer Control/Status                 32
0x4   CLO    System Timer Counter Lower 32 bits    32
0x8   CHI     System Timer Counter Higher 32 bits    32
0xc   C0       System Timer Compare 0                       32
0x10 C1       System Timer Compare 1                       32
0x14 C2       System Timer Compare 2                       32
0x18 C3       System Timer Compare 3                       32

<!--more-->

1.CS寄存器的第四位分别为 M0 M1 M2 M3 。Mn寄存器置1，表示打开Cn。31:4 bit 无用

2.CLO CHI 分别是timer的低32位和高32位。

3.C0-C3 四个定时器

&nbsp;