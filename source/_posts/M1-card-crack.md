---
title: 热水卡破解
tags:
  - 122U
  - Mifare1
  - RFID
  - 热水卡
  - 破解
id: 18
categories:
  - Hacking
date: 2014-11-22 13:18:36
---

今天遇到点烦心事，只看了GDT（Global Descriptor Table）相关的内容，没有继续完成操作系统的引导部分。不过晚上热水卡没有钱了，刚好今天OS没有大的进展，顿时萌生了“自力更生，丰衣足食”的良好念头 :-)   。

<!--more-->

M1卡，全程NXP Mifare1 卡。现在日常接触到的大部分都是M1卡。学校的所有卡，包括公交卡、甚至学校饭卡。

**  0X00  先从M1卡的结构说起吧**

*   存储结构
*   M1卡分为16个扇区，每个扇区由4块（块0、块1、块2、块3）组成，（我们也将16个扇区的64个块按绝对地址编号为0~63，存贮结构如下图所示：
<table>
<tbody>
<tr>
<td width="60"></td>
<td width="48"></td>
<td width="204"></td>
<td width="72"></td>
<td width="48"></td>
</tr>
<tr>
<td width="60"></td>
<td width="48">块0</td>
<td width="204"></td>
<td width="72">数据块</td>
<td width="48">0</td>
</tr>
<tr>
<td width="60">扇区0</td>
<td width="48">块1</td>
<td width="204"></td>
<td width="72">数据块</td>
<td width="48">1</td>
</tr>
<tr>
<td width="60"></td>
<td width="48">块2</td>
<td width="204"></td>
<td width="72">数据块</td>
<td width="48">2</td>
</tr>
<tr>
<td width="60"></td>
<td width="48">块3</td>
<td width="204">密码A   存取控制   密码B</td>
<td width="72">控制块</td>
<td width="48">3</td>
</tr>
<tr>
<td width="60"></td>
<td width="48">块0</td>
<td width="204"></td>
<td width="72">数据块</td>
<td width="48">4</td>
</tr>
<tr>
<td width="60">扇区1</td>
<td width="48">块1</td>
<td width="204"></td>
<td width="72">数据块</td>
<td width="48">5</td>
</tr>
<tr>
<td width="60"></td>
<td width="48">块2</td>
<td width="204"></td>
<td width="72">数据块</td>
<td width="48">6</td>
</tr>
<tr>
<td width="60"></td>
<td width="48">块3</td>
<td width="204">密码A   存取控制   密码B</td>
<td width="72">控制块</td>
<td width="48">7</td>
</tr>
<tr>
<td width="60"></td>
<td width="48"> 一直到15</td>
<td width="204"></td>
<td width="72"></td>
<td width="48"></td>
</tr>
</tbody>
</table>

*   第0扇区的块0（即绝对地址0块），它用于存放厂商代码，已经固化，不可更改。
*   每个扇区的块0、块1、块2为**数据块**，可用于存贮数据。
**数据块**可作两种应用：

*   用作一般的数据保存，可以进行**读**、**写**操作。
*   用作数据值，可以进行**初始化值、加值、减值、读值**操作。
*   每个扇区的块3为**控制块**，包括了密码A、存取控制、密码B。具体结构如下：
<table>
<tbody>
<tr>
<td width="402">
<table width="100%">
<tbody>
<tr>
<td>A0 A1 A2 A3 A4 A5   FF 07 80 69     B0 B1 B2 B3 B4 B5</td>
</tr>
</tbody>
</table>
</td>
</tr>
</tbody>
</table>
密码A（6字节）  存取控制（4字节） 密码B（6字节）

每个扇区的密码和存取控制都是独立的，可以根据实际需要设定各自的密码及存取控制。存取控制为4个字节，共32位，扇区中的每个块（包括数据块和控制块）的存取条件是由密码和存取控制共同决定的，在**存取控制**中每个块都有相应的**三个控制位**,定义如下：

块0：   C10   C20   C30

块1：   C11   C21   C31

块2：   C12   C22   C32

块3：   C13   C23   C33

三个控制位以正和反两种形式存在于存取控制字节中，决定了该块的访问权限（如

进行减值操作必须验证KEY A，进行加值操作必须验证KEY B，等等）。三个控制

位在存取控制字节中的位置，以块0为例：

&nbsp;

对块0的控制：

bit  7    6     5       4     3      2      1      0
<table>
<tbody>
<tr>
<td width="72">字节6</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48">C20_b</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48">C10_b</td>
</tr>
<tr>
<td width="72">字节7</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48">C10</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48">C30_b</td>
</tr>
<tr>
<td width="72">字节8</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48">C30</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48">C20</td>
</tr>
<tr>
<td width="72">字节9</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
</tr>
</tbody>
</table>
( 注： C10_b表示C10取反 )

&nbsp;

存取控制（4字节，其中字节9为备用字节）结构如下所示：

bit  7    6      5       4     3      2      1      0
<table>
<tbody>
<tr>
<td width="72">字节6</td>
<td width="48">C23_b</td>
<td width="48">C22_b</td>
<td width="48">C21_b</td>
<td width="48">C20_b</td>
<td width="48">C13_b</td>
<td width="48">C12_b</td>
<td width="48">C11_b</td>
<td width="48">C10_b</td>
</tr>
<tr>
<td width="72">字节7</td>
<td width="48">C13</td>
<td width="48">C12</td>
<td width="48">C11</td>
<td width="48">C10</td>
<td width="48">C33_b</td>
<td width="48">C32_b</td>
<td width="48">C31_b</td>
<td width="48">C30_b</td>
</tr>
<tr>
<td width="72">字节8</td>
<td width="48">C33</td>
<td width="48">C32</td>
<td width="48">C31</td>
<td width="48">C30</td>
<td width="48">C23</td>
<td width="48">C22</td>
<td width="48">C21</td>
<td width="48">C20</td>
</tr>
<tr>
<td width="72">字节9</td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
</tr>
</tbody>
</table>
( 注： _b表示取反 )

&nbsp;

6、**数据块**（块0、块1、块2）的存取控制如下：

&nbsp;
<table>
<tbody>
<tr>
<td colspan="3" width="144">  控制位（X=0..2）</td>
<td colspan="4" width="312">         访 问 条 件 （对数据块 0、1、2）</td>
</tr>
<tr>
<td width="48">C1X</td>
<td width="48">C2X</td>
<td width="48">C3X</td>
<td width="60"> Read</td>
<td width="60"> Write</td>
<td width="72">Increment</td>
<td width="120">Decrement, transfer,Restore</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">0</td>
<td width="48">0</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
<td width="72">KeyA|B</td>
<td width="120">KeyA|B</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">1</td>
<td width="48">0</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="72">Never</td>
<td width="120">Never</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">0</td>
<td width="48">0</td>
<td width="60">KeyA|B</td>
<td width="60">KeyB</td>
<td width="72">Never</td>
<td width="120">Never</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">1</td>
<td width="48">0</td>
<td width="60">KeyA|B</td>
<td width="60">KeyB</td>
<td width="72">KeyB</td>
<td width="120">KeyA|B</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">0</td>
<td width="48">1</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="72">Never</td>
<td width="120">KeyA|B</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">1</td>
<td width="48">1</td>
<td width="60">KeyB</td>
<td width="60">KeyB</td>
<td width="72">Never</td>
<td width="120">Never</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">0</td>
<td width="48">1</td>
<td width="60">KeyB</td>
<td width="60">Never</td>
<td width="72">Never</td>
<td width="120">Never</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">1</td>
<td width="48">1</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="72">Never</td>
<td width="120">Never</td>
</tr>
</tbody>
</table>
（KeyA|B 表示密码A或密码B，Never表示任何条件下不能实现）

&nbsp;

例如：当块0的存取控制位C10 C20 C30=1 0 0时，验证密码A或密码B正确后可读；

验证密码B正确后可写；不能进行加值、减值操作。

&nbsp;

7、**控制块**块3的存取控制与**数据块**（块0、1、2）不同，它的存取控制如下：

&nbsp;
<table>
<tbody>
<tr>
<td width="48"></td>
<td width="48"></td>
<td width="48"></td>
<td colspan="2" width="120">**密码A**</td>
<td colspan="2" width="120">**存取控制**</td>
<td colspan="2" width="120">**密码B**</td>
</tr>
<tr>
<td width="48">C13</td>
<td width="48">C23</td>
<td width="48">C33</td>
<td width="60">Read</td>
<td width="60">Write</td>
<td width="60">Read</td>
<td width="60">Write</td>
<td width="60">Read</td>
<td width="60">Write</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">0</td>
<td width="48">0</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">1</td>
<td width="48">0</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">0</td>
<td width="48">0</td>
<td width="60">Never</td>
<td width="60">KeyB</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">KeyB</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">1</td>
<td width="48">0</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">Never</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">0</td>
<td width="48">1</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
<td width="60">KeyA|B</td>
</tr>
<tr>
<td width="48">0</td>
<td width="48">1</td>
<td width="48">1</td>
<td width="60">Never</td>
<td width="60">KeyB</td>
<td width="60">KeyA|B</td>
<td width="60">KeyB</td>
<td width="60">Never</td>
<td width="60">KeyB</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">0</td>
<td width="48">1</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">KeyB</td>
<td width="60">Never</td>
<td width="60">Never</td>
</tr>
<tr>
<td width="48">1</td>
<td width="48">1</td>
<td width="48">1</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">KeyA|B</td>
<td width="60">Never</td>
<td width="60">Never</td>
<td width="60">Never</td>
</tr>
</tbody>
</table>
例如：当块3的存取控制位C13 C23 C33=1 0 0时，表示：

密码A：不可读，验证KEYA或KEYB正确后，可写（更改）。

存取控制：验证KEYA或KEYB正确后，可读、可写。

密码B：验证KEYA或KEYB正确后，可读、可写。

知道这些东西已经足够了，就看怎么利用了。

**0x01 破解key，读取数据**

大多数卡中的数据除了第一扇区记录卡的信息外，剩下的15个扇区只有一个扇区记录金额和校验信息。剩下的扇区都是空数据。keyA keyB都是默认的。关于破解的算法08年国外已经有人公布 。运气好的话用Mifare Classic Offline Cracker就可以读到卡中16个扇区的数据。

![](http://ccwyy.img43.wal8.com/img43/507648_20150116191603/14217545389.png)

但读到数据只是第一步，还需要知道那里存的是金额哪里是校验位，以及校验算法

**0x02  破解校验算法**

现在只看金额和校验数据的8个字节，通过两张卡都刷了两次得到以下数据

卡A

01  14   03  20  03  bd    a8   f1          金额 957

01  14  03  20  03   bc    a9   f1          金额 956

卡B

01  14  03  20  20   40   56   f1           金额 64

01  14  03  20  20    3f    29   f1           金额 63

这里需要对十六进制数据比较敏感。 0x03BD 两个字节就是 957    0x03bc是 956

* * *

2014年12月7日10:19:27

*   上次的工作只进行了一半，只知道了保存金额的位置。那就是0x384  0x285 这两个地址的数值。而且还是大端模式的存储。再来分析下。
01  14   03  20  03  bd    a8   f1

01  14  03  20  03   bc    a9   f1

01  14  03  20  20   40   56   f1

01  14  03  20  20    3f    29   f1

*   似乎前四个字节和最后一个字节的数值不变。那么现在只有一个字节的数据定义不清楚了。 这就是传说中的校验数据。这一字节的数据验证金额是否合法。不合法的话就算前面两个字节再大，也会不认得。 那么怎么破解校验呢。
03  bd    a8                03   bc    a9               20   40   56                20    3f    29

这三次的校验字节分别是 0xa8  0xa9  0x56   0x29 这和前面两个字节的数据有什么关系！！  还是看不出来 。还是采集的数据太少了，我再找几张卡去。

A 、B、C、D张卡共刷了50次左右的数据

//卡A   +1
01 14 03 20 03 bd a8 f1
01 14 03 20 03 bc a9 f1

01 14 03 20 01 d4 c3 f1
01 14 03 20 01 d3 c4 f1
01 14 03 20 01 d2 c5 f1
01 14 03 20 01 d1 c6 f1
01 14 03 20 01 d0 c7 f1
01 14 03 20 01 cf d8 f1
01 14 03 20 01 ce d9 f1

01 14 03 20 01 ba ad f1
01 14 03 20 01 b9 ae f1
01 14 03 20 01 b8 af f1

//卡B   +3-1+3-1+3-1
01 14 03 20 20 40 56 f1

01 14 03 20 20 3f 29 f1
01 14 03 20 20 3e 28 f1
01 14 03 20 20 3d 2b f1
01 14 03 20 20 3c 2a f1
01 14 03 20 20 3b 2d f1
01 14 03 20 20 3a 2c f1
01 14 03 20 20 39 2f f1

01 14 03 20 20 1c 0a f1
01 14 03 20 20 1b 0d f1
01 14 03 20 20 1a 0c f1

//卡C    +3-1-5-1+3-1-5-1+3
01 14 03 20 04 b6 a4 f1
01 14 03 20 04 b5 a7 f1
01 14 03 20 04 b4 a6 f1
01 14 03 20 04 b3 a1 f1
01 14 03 20 04 b2 a0 f1
01 14 03 20 04 b1 a3 f1
01 14 03 20 04 b0 a2 f1
01 14 03 20 04 af bd f1
01 14 03 20 04 ae bc f1
01 14 03 20 04 ad bf f1

//卡D     -1+3-1+3
01 14 03 20 09 8f 90 f1

01 14 03 20 08 5d 43 f1
01 14 03 20 08 5c 42 f1
01 14 03 20 08 5b 45 f1
01 14 03 20 08 5a 44 f1
01 14 03 20 08 59 47 f1

01 14 03 20 08 3a 24 f1
01 14 03 20 08 39 27 f1

01 14 03 20 08 38 26 f1

*   看了几个小时，找到了规律。红色部分 3 -&gt;2   1-&gt;0 。 再看看其他的数据，都符合这个规律。0&lt;-&gt;1,2&lt;-&gt;3,4&lt;-&gt;5 ...... E&lt;-&gt;F。
&nbsp;

好了，校验字节的高四位已经明了。只剩下低四位了。 又看了几个小时，找到了最后一个字母的规律。结果是每张卡的校验算法都是不一样的。

*   卡A
//卡A   +1

01 14 03 20 01 d4 c3 f1
01 14 03 20 01 d3 c4 f1
01 14 03 20 01 d2 c5 f1
01 14 03 20 01 d1 c6 f1
01 14 03 20 01 d0 c7 f1
01 14 03 20 01 cf d8 f1
01 14 03 20 01 ce d9 f1

当金额每次减一 即01 d4、01 d3、 01 d1 、01 d0、 01 cf、01 ce   校验字节的低四位分别是 3、4、5、6、7、8、9

*   卡B
01 14 03 20 20 3f 29 f1
01 14 03 20 20 3e 28 f1
01 14 03 20 20 3d 2b f1
01 14 03 20 20 3c 2a f1
01 14 03 20 20 3b 2d f1
01 14 03 20 20 3a 2c f1
01 14 03 20 20 39 2f f1

当金额每次减一，校验字节的低四位分别是 9、8、b、a、d、c、f       规律 ：+3-1+3-1+3-1...........

*   卡C
01 14 03 20 04 b6 a4 f1
01 14 03 20 04 b5 a7 f1
01 14 03 20 04 b4 a6 f1
01 14 03 20 04 b3 a1 f1
01 14 03 20 04 b2 a0 f1
01 14 03 20 04 b1 a3 f1
01 14 03 20 04 b0 a2 f1
01 14 03 20 04 af bd f1
01 14 03 20 04 ae bc f1
01 14 03 20 04 ad bf f1

规律 ：+3-1-5-1+3-1-5-1+3 ...........

*   卡D
01 14 03 20 08 5d 43 f1
01 14 03 20 08 5c 42 f1
01 14 03 20 08 5b 45 f1
01 14 03 20 08 5a 44 f1
01 14 03 20 08 59 47 f1

规律 ：-1+3-1+3 ...........

为什吗只有B和D的校验算法是一样的！！！百思不得其解 ;-(      想了想应该有下面两个原因

1.  校验结果很有可能和每张卡的ID有关，每张卡的ID在第0扇区存着，按照规定是不能改的。
2.  <del>校验结果和0x384 处的的值有关。 </del>想想还是不可能，因为在0x384 值不变的情况下 0x386 第四位的值已经变了。
**  0X03 总结**

现在已经可以改写卡的数据了，改完之后计算出相应的校验字节，写入0x386就可以。麻烦的是没有一个统一的算法算。只能先把每张卡的规律找出来。我觉得肯定有统一的算法，只是我没有总结出来而已。要分析清楚，需要采集大量的数据，没那个时间。反正目达到就好。有兴趣的同学还可以继续研究，比如说你的洗澡卡，洗衣卡，矿泉水卡，公交卡、、、

写入数据的时候需要keyA 或 keyB  。 就这样。

我可是好人，没有做坏事。为了免去不必要的麻烦，还是毕业之后在公布 吧 。