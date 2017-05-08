---
title: Linux 段错误调试
id: 218
categories:
  - Coding
  - Linux
date: 2015-09-22 17:39:12
tags:
---

今天修改了一个段错误的bug。

## 第一步  用dmesg命令查看最近的段错误。

[Mon Sep 14 15:11:50 2015] class[4200]: segfault at 0 ip 00007fcac993dbdd sp 00007fcaca4f5ac8 error 4 in libzealot.so[7fcac98ea000+6c000]

segfault at 0   表示段错误发生的地址。这里是0，可以判断是给地址为0处读取或者写入一个值。也就是出现了空指针的问题

*   ip 00007fcac993dbdd   这是错误发生的时候ip的位置，根据这个值可以定位错误发生的位置。
*   sp 00007fcaca4f5ac8    这是错误发生的时候sp的位置。调试过程中没有用到。
*   error 4  错误类型，在这里等于4 也就是bit2 == 1 user-mode access，bit1 == 0 read access，bit0 == 0 no page found。 结合ip的值可以判断是因为访问了一个空指针（read）。

    <span id="cjabiokabamngnbigeeibddnihgllkggmousedown-target-element" class="com">    /*
         * Page fault error code bits:
         *
         *   bit 0 ==    0: no page found       1: protection fault
         *   bit 1 ==    0: read access         1: write access
         *   bit 2 ==    0: kernel-mode access  1: user-mode access
         *   bit 3 ==                           1: use of reserved bit detected
         *   bit 4 ==                           1: fault was an instruction fetch
         */
    </span>
<!-- more -->
*   libzealot.so[7fcac98ea000+6c000]   错误发生的动态库。7fcac98ea000表示这次动态库加载的内存基地址，6c000是大小。

## 第二部  反汇编

objdump -d zealot.so &gt; zealot.dump

## 第三部 定位

0x00007fcac993dbdd   - 0x7fcac98ea000 = 0x53bdd。在zealot.dump中找53bdd 定位到代码

可以看到此处代码是 mov    (%rax),%rax 。此处rax为零导致段错。再去分析C代码，就可以找到问题。

&nbsp;

注意  如果段错发生在动态库中，由于每次动态库加载的地址都不一样。 若 ip - 动态库基地址值不变，说明出问题的位置是同一处。

&nbsp;

参考

[http://stackoverflow.com/questions/2549214/interpreting-segfault-messages  ](http://stackoverflow.com/questions/2549214/interpreting-segfault-messages)

[http://blog.163.com/longsu2010@yeah/blog/static/17361234820122761525799/  ](http://blog.163.com/longsu2010@yeah/blog/static/17361234820122761525799/)

&nbsp;

&nbsp;