---
title: Raspberry Pi GPU 编程
tags:
  - GPU
  - mailbox
  - 图像
  - 汇编
id: 80
categories:
  - Operating System
date: 2014-12-22 10:02:17
---

要把我的操作系统移植到Raspberry Pi 上了，所以最近一直在看ARM的东西。找来找去只找到一个有点用的 [http://www.cl.cam.ac.uk/projects/raspberrypi/tutorials/os/](http://www.cl.cam.ac.uk/projects/raspberrypi/tutorials/os/screen01.html#gs  "http://www.cl.cam.ac.uk/projects/raspberrypi/tutorials/os/screen01.html#gs")   剑桥大学的一份教程，虽然离一个完整的内核还差得很远，但对我还算是有点用。之前一直担心的GPU的问题也解决了，也是我找到的唯一一份和Raspberry Pi GPU编程相关的东西了。<!--more-->

这次用到的是 2bit的 High Color   —  Use 16 bits to store each pixel, the first 5 bit representing the intensity of the red channel, the next 6 bits representing the intensity of the green channel and the final 5 bits representing the intensity of the blue channel

<!-- [if IE 6]>

	<link rel="stylesheet" href="../../../../style/ie6.css" type="text/css" media="screen" /><script type="text/javascript" src="http://www.cam.ac.uk/global/js/minmax.js"></script><![endif]--><!-- [if IE 7]>
	<link rel="stylesheet" href="../../../../style/ie7.css" type="text/css" media="screen" /><![endif]--><script src="//www.google-analytics.com/analytics.js" async=""></script><script src="script.js" type="text/javascript" language="javascript"></script><script>// <![CDATA[
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');
  ga('create', 'UA-47341509-1', 'cam.ac.uk');
  ga('send', 'pageview');
// ]]></script>
<div id="container">
<div id="content">
<div id="content-primary">
<div id="contentAll">
<div id="pageAll">
<div id="pageBody">  这么奇怪的启动方式。加电后GPU先启动，然后 ARM核才启动，两个处理器通过“邮件”的方式通信，详见</div>
<div>    The Raspberry Pi has a very special and rather odd relationship with it's graphics processor. On the Raspberry Pi, the graphics processor actually runs first, and is responsible for starting up the main processor. This is very unusual. Ultimately it doesn't make too much difference, but in many interactions, it often feels like the processor is secondary, and the graphics processor is the most important. The two communicate on the Raspberry Pi by what is called the 'mailbox'. Each can deposit mail for the other, which will be collected at some future point and then dealt with. We shall use the mailbox to ask the graphics processor for an address. The address will be a location to which we can write the pixel colour information for the screen, called a frame buffer, and the graphics card will regularly check this location, and update the pixels on the screen appropriately.</div>
<div></div>
<div><!-- [if IE 6]>

	<link rel="stylesheet" href="../../../../style/ie6.css" type="text/css" media="screen" /><script type="text/javascript" src="http://www.cam.ac.uk/global/js/minmax.js"></script><![endif]--><!-- [if IE 7]>
	<link rel="stylesheet" href="../../../../style/ie7.css" type="text/css" media="screen" /><![endif]--><script src="//www.google-analytics.com/analytics.js" async="" type="mce-no/type"></script><script src="script.js" type="mce-text/javascript" language="javascript"></script><script type="mce-no/type">// <![CDATA[
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');
  ga('create', 'UA-47341509-1', 'cam.ac.uk');
  ga('send', 'pageview');
// ]]></script>
<div id="container">
<div id="content">
<div id="content-primary">
<div id="contentAll">
<div id="pageAll">
<div id="pageBody">
<table><caption>Table  - Mailbox Addresses</caption>
<thead>
<tr>
<th>Address</th>
<th>Size / Bytes</th>
<th>Name</th>
<th>Description</th>
<th>Read / Write</th>
</tr>
</thead>
<tbody>
<tr>
<td>2000B880</td>
<td>4</td>
<td>Read</td>
<td>Receiving mail.</td>
<td>R</td>
</tr>
<tr>
<td>2000B890</td>
<td>4</td>
<td>Poll</td>
<td>Receive without retrieving.</td>
<td>R</td>
</tr>
<tr>
<td>2000B894</td>
<td>4</td>
<td>Sender</td>
<td>Sender information.</td>
<td>R</td>
</tr>
<tr>
<td>2000B898</td>
<td>4</td>
<td>Status</td>
<td>Information.</td>
<td>R</td>
</tr>
<tr>
<td>2000B89C</td>
<td>4</td>
<td>Configuration</td>
<td>Settings.</td>
<td>RW</td>
</tr>
<tr>
<td>2000B8A0</td>
<td>4</td>
<td>Write</td>
<td>Sending mail.</td>
<td>W</td>
</tr>
</tbody>
</table>
&nbsp;

发送：
<div id="container">
<div id="content">
<div id="content-primary">
<div id="contentAll">
<div id="pageAll">
<div id="pageBody">

1.  The sender waits until the Status field has a 0 in the top bit.
2.  The sender writes to Write such that the lowest 4 bits are the mailbox to write to, and the upper 28 bits are the message to write.
接收:

1.  The receiver waits until the Status field has a 0 in the 30th bit.
2.  The receiver reads from Read.
3.  The receiver confirms the message is for the correct mailbox, and tries again if not.
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
<!-- [if IE 6]>

	<link rel="stylesheet" href="../../../../style/ie6.css" type="text/css" media="screen" /><script type="text/javascript" src="http://www.cam.ac.uk/global/js/minmax.js"></script><![endif]--><!-- [if IE 7]>
	<link rel="stylesheet" href="../../../../style/ie7.css" type="text/css" media="screen" /><![endif]--><script src="//www.google-analytics.com/analytics.js" async="" type="mce-no/type"></script><script src="script.js" type="mce-text/javascript" language="javascript"></script><script type="mce-no/type">// <![CDATA[
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');
  ga('create', 'UA-47341509-1', 'cam.ac.uk');
  ga('send', 'pageview');
// ]]></script>