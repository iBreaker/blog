---
title: Bochs
tags:
  - A32
  - Bochs
  - qemu
  - 模拟器
  - 汇编
id: 10
categories:
  - Operating System
date: 2014-11-19 19:31:53
---

刚刚编译好的新鲜Bochs出炉了。编译这个还真是费尽周折。先从Bochs官网下载win32 编译好的程序。打开发现竟然不能模拟80386CPU，可能是80386太古老了吧。 找了半天才在stackoverflow上看到要重新编译。下载源码，上传到RaspberryPi上加--enable-cpu-level=3  config。然后再弄到win上用VS2013编译。

<!--more-->

加载最新的bios不兼容，下载了一个很旧的bochs版本在里面找到了bios镜像。可以运行我的MBR了。

bochs 常用的命令：

# 

&nbsp;

From here, you may use the following commands:
<div class="SECTION">
<pre class="SCREEN">  c                           continue executing
  s     [count]               execute count instructions, default is 1
  Ctrl-C                      stop execution, and return to command line prompt
  Ctrl-D                      if at empty line on command line, exit
  q                           quit debugger and execution

  vb seg:off                  Set a virtual address instruction breakpoint
  lb addr                     Set a linear address instruction breakpoin
  pb     [*] addr             Set a physical address instruction breakpoin
  b      [*] addr
  info break                  Display state of all current breakpoints
  bpe    n                    Enable a breakpoint
  bpd    n                    Disable a breakpoint
  d      n                    Delete a breakpoint</pre>
</div>
<div class="SECTION">
<pre class="SCREEN">  watch r     addr            Insert a read watch point at physical address `addr`
  watch w     addr            Insert a write watch point at physical address `addr`
  watch                       Display state of current memory watchpoints
  watch stop                  Stop simulation when a watchpoint is encountered (default)
  watch continue              Do not stop simulation when a watchpoint is encountered
  unwatch addr                Remove watchpoint to specific physical address
  unwatch                     Remove all watch points
  trace-mem on/off            Enable/Disable memory access tracing</pre>
</div>
<div class="SECTION">
<pre class="SCREEN">  x  /nuf addr      Examine memory at linear address addr
  xp /nuf addr      Examine memory at physical address addr
     n              Count of how many units to display
     u              Unit size; one of
                      b Individual bytes
                      h Halfwords (2 bytes)
                      w Words (4 bytes)
                      g Giant words (8 bytes)
                      NOTE: these are *not* typical Intel nomenclature sizes,
                            but they are consistent with GDB convention.
     f              Printing format.  one of
                      x Print in hexadecimal
                      d Print in decimal
                      u Print in unsigned decimal
                      o Print in octal
                      t Print in binary

    n, f, and u are optional parameters.  u and f default to the last values
    you used, or to w(words) and x(hex) if none have been supplied.
    n currently defaults to 1\.  If none of these optional parameters are
    used, no slash should be typed.  addr is also optional.  If you don't
    specify it, it will be the value the next address (as if you had
    specified n+1 in the last x command).

  setpmem addr datasize val    Set physical memory location of size
                               datasize to value val.
  writemem                     dump a number of bytes of virtual memory starting from
                               the specified linear address into a file
  crc  addr1  addr2            Show CRC32 for physical memory range addr1..addr2</pre>
</div>
<div class="SECTION">
<pre class="SCREEN">  r|reg|regs|registers         List of CPU integer registers and their contents
  sreg                         Show segment registers and their contents
  dreg                         Show debug registers and their contents
  creg                         Show control registers and their contents
  info cpu                     List of all CPU registers and their contents
  info eflags                  Show decoded EFLAGS register
  info break                   Information about current breakpoint status
  set reg = expr
  trace on                    Disassemble every executed instruction. Note</pre>
</div>
<div class="SECTION">
<pre class="SCREEN">                              that instructions which caused exceptions are
                              not really executed, and therefore not traced.
  trace off                   Disable instruction tracing.
  具体详见  **[bochs documention](http://bochs.sourceforge.net/doc/docbook/ "bochs documention") 

2014年11月20日17:54:13 
 ** 第一个汇编函数可以运行了
![](http://ccwyy.img43.wal8.com/img43/507648_20150116191603/142175554866.png)
    现在十一点半，顺利从磁盘加载512KB数据到内存0x20000-0x9ffff。本来想从0x10000处加载的，
可是在0x10000附近开始加载只能读大概61个扇区，然后就莫名其妙的进入死循环了。肯定和其实地
址有关，但是在附近换了个地址也只能读61扇区。 0x20000开始就没有问题了。莫名其妙。今天完成
了两个函数 printf 和 D2M 。还算顺利。

</pre>
</div>