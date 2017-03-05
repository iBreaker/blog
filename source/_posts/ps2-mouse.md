---
title: PS2鼠标中断信号的处理
tags:
  - PS2
  - 协议
  - 鼠标
id: 58
categories:
  - Operating System
date: 2014-12-05 22:02:52
---

键盘数据已经可以正常接收了，鼠标的处理比键盘要稍微复杂一点。鼠标每次的信号是三个字节，第一字节0-2分别保存着鼠标左键右键中键是否按下的标志。3位一直为1。根据这个特性可以判断接收到的字节是否是鼠标第一字节的信号。4 5 两位分别表示鼠标 x，y位置是否发生位移。 6 7  是 x y溢出标志位。<!--more-->

判断是第一个字节后依次接收第二，三个字节。然后将三个字节一起处理。第二字节和第三字节保存着x 位移 和y 位移的补码。也就是说x y 都是带符号的，正号表示正方向，负号表示负方向。X ，Y 的正方向分别为 左 和 下。 这里Y的正方向和我们平时的习惯正好相反。所以计算出来后要加负号。

还有要注意的就是在鼠标初始化完成的时候会产生一个鼠标中断，0x7A 。要将这一个字节的数据过滤掉，不能算在鼠标信号中。找了一份PS2的协议，下面是鼠标三个字节数据的定义。表格果然直观多了。

可以处理鼠标信号后要做的第一件事就是让鼠标动起来。 代码很简单，当前坐标加上位移，计算出新坐标重回鼠标就可以。桌面的重绘还没有做。所以会出现 win 移动窗口的特效，真是炫酷 ;-) 。

![](http://ccwyy.img43.wal8.com/img43/507648_20150116191603/14217562038.jpeg)

**Movement Data Packet:**

位移数据包

The standard PS/2 mouse sends movement (and button) information to the host using the following 3-byte packet (4):

标准的 PS/2 鼠标发送位移和按键信息给主机采用如下的 3 字节数据包格式见本章脚注 <sub>4           </sub>

&nbsp;

Bit 7               Bit 6               Bit 5               Bit 4               Bit 3               Bit 2               Bit 1               Bit 0
<table width="567">
<tbody>
<tr>
<td width="71">Y overflow</td>
<td width="71"> X overflow</td>
<td width="71">Y sign bit</td>
<td width="71">X sign bit</td>
<td width="71">Always 1</td>
<td width="71">Middle Btn</td>
<td width="71">Right Btn</td>
<td width="71">Left Btn</td>
</tr>
<tr>
<td colspan="2" width="142"></td>
<td width="71"></td>
<td colspan="2" width="142">X Movement</td>
<td width="71"></td>
<td width="71"></td>
<td width="71"></td>
</tr>
<tr>
<td colspan="2" width="142"></td>
<td width="71"></td>
<td colspan="2" width="142">Y Movement</td>
<td width="71"></td>
<td width="71"></td>
<td width="71"></td>
</tr>
</tbody>
</table>
Byte 1 Byte 2

Byte 3

&nbsp;

The movement counters are 9-bit 2's complement integers, where the most significant bit appears as a sign bit in Byte 1 of the movement data packet. These counters are updated when the mouse reads its input and finds movement has occurred. Their value is the amount of movement that has occurred since the last movement data packet was sent to the host (ie, after a packet is sent to the host, the movement counters are reset.) The range of values that can be expressed by the movement counters is -255 to +255\. If this range is exceeded, the appropriate overflow bit is set and the counter is not incremented/decremented until it is reset.

位移计数器是一个 9 位 2 的补码整数 它的 高位作为符号位出现在位移数据包的第一个字节里 这些计数器在鼠标读取输入发现有位移时被更新 这些值是自从 后一次发送位移数据包给主机后位移的累计量即 后一次包发给主机后 位移计数器被复位  位移计数器可表示的值的范围是-255 到+255 如果超过了范围 相应的溢出位就被设置 并且在复位前 计数器不会增减

&nbsp;

As I mentioned earlier, the movement counters are reset whenever a movement data packet is successfully sent to the host. They are also reset after the mouse receives any command from the host other than the "Resend" (0xFE) command.

正如我前面提及的 一旦位移数据包成功地发送给主机 位移计数器就会复位 同样鼠标在收到主机不是Resend 0xFE 命令外的其他命令 计数器也会复位