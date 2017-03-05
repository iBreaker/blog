---
title: 列表，元组，字符串互相转换
tags:
  - print
  - python
id: 179
categories:
  - Python
date: 2015-05-29 19:43:58
---

列表，元组，字符串之间的转化通过join(), str(), list(), tuple() 这四个函数实现。

*   用list可以把字符串和元组转化为列表
<pre>&gt;&gt;&gt; demo_str = 'test' 
&gt;&gt;&gt; demo_tuple = ('t','e','s','t')
&gt;&gt;&gt;demo_list = ['t','e','s','t']
&gt;&gt;&gt; temp = list(demo_tuple)
&gt;&gt;&gt; type(temp)
&lt;type 'list'&gt;
&gt;&gt;&gt; temp = list(demo_str)
&gt;&gt;&gt; type(temp)
&lt;type 'list'&gt;
</pre>
<!--more-->

*   用tuple() 可以将字符串和列表转化为元组
<pre>&gt;&gt;&gt; demo_str = 'test' 
&gt;&gt;&gt; demo_tuple = ('t','e','s','t')
&gt;&gt;&gt;demo_list = ['t','e','s','t']
&gt;&gt;&gt; temp = tuple(demo_str)
&gt;&gt;&gt; type(temp)
&lt;type 'tuple'&gt;
&gt;&gt;&gt; temp = tuple(demo_list)
&gt;&gt;&gt; type(temp)
&lt;type 'tuple'&gt;
</pre>

*   用str() 可以将字符串和列表转化为字符串
<pre>&gt;&gt;&gt; demo_str = 'test' 
&gt;&gt;&gt; demo_tuple = ('t','e','s','t')
&gt;&gt;&gt;demo_list = ['t','e','s','t']
&gt;&gt;&gt; temp = str(demo_list)
&gt;&gt;&gt; type(temp)
&lt;type 'str'&gt;
&gt;&gt;&gt; temp = str(demo_tuple)
&gt;&gt;&gt; type(temp)
&lt;type 'str'&gt;
</pre>

### <span id=".E6.B3.A8.E6.84.8F" class="mw-headline">注意</span>

用str()转换的字符串不能用print()函数以字符串形式显示
<pre>&gt;&gt;&gt; demo_str = 'test' 
&gt;&gt;&gt; demo_tuple = ('t','e','s','t')
&gt;&gt;&gt;demo_list = ['t','e','s','t']
&gt;&gt;&gt; temp = str(demo_list)
&gt;&gt;&gt; type(temp)
&lt;type 'str'&gt;
&gt;&gt;&gt;print (temp)
['t', 'e', 's', 't']
&gt;&gt;&gt; temp = str(demo_tuple)
&gt;&gt;&gt; type(temp)
&lt;type 'str'&gt;
&gt;&gt;&gt;print (temp)
('t', 'e', 's', 't')
</pre>
对于这种问题要用join()函数处理
<pre>&gt;&gt;&gt; demo_str = 'test' 
&gt;&gt;&gt; demo_tuple = ('t','e','s','t')
&gt;&gt;&gt;demo_list = ['t','e','s','t']
&gt;&gt;&gt; temp = ''.join(demo_list)
&gt;&gt;&gt; type(temp)&lt;type 'str'&gt;
&gt;&gt;&gt;print (temp)
test
&gt;&gt;&gt; temp = ''.join(demo_tuple)
&gt;&gt;&gt; type(temp)
&lt;type 'str'&gt;
&gt;&gt;&gt;print (temp)
test
</pre>
**用join()和str()生成的都是字符串类型的，但为什么用print 输出的结果不同？**

&nbsp;

* * *

本文地址: [http://blog.0x7c00.cn/?p=179](http://blog.0x7c00.cn/?p=179)                                                                              By Breaker

同时被收录在: [Breaker's Wiki ](http://www.0x7c00.cn/index.php?title=Python_%E7%AC%94%E8%AE%B0 "Brekaer")                                                                                             QQ: 791628659