---
title: MediaWiki
tags:
  - MediaWiki
  - 表格
  - 链接
id: 190
categories:
  - Coding
date: 2015-05-29 21:03:02
---

打算用MediaWiki搭建一个知识库，作为个人文档库，将博客里面写的东西整理后放入wiki永久保存。博客和wiki最主要的区别是wiki更有利于存档以及以后查看，而博客比较随意，可以记录任何事情。

这篇文章主要记录MediaWiki的基本用法，觉得一篇文章够用了，所以就没有添加Wiki的分类目录。

# <span class="mw-headline">一 链接 </span>

## 内部链接

<!--more-->
<pre>当名称与链接目标相同时可简写为：[[首页|]]

当名称与链接目标不同时：[[首页|回到站点首页]]</pre>

## 外部连接

<pre>[http://www.0x7c00.cn Breaker's Wiki]</pre>
&nbsp;

# 二 表格

<pre>{| _参数 = _&lt;table _参数_&gt;
|} = &lt;/table&gt;</pre>
<pre>|单元1                         
|单元2 = |单元1||单元2||单元3 = &lt;td&gt;单元1&lt;/td&gt;&lt;td&gt;单元2&lt;/td&gt;&lt;td&gt;单元3&lt;/td&gt;
|单元3</pre>
<pre>|- ''参数''  =  |-------------- ''参数''  =   &lt;tr 参数&gt;</pre>
<pre>|+ 标题  =  &lt;caption&gt;标题&lt;/caption&gt;
|+ ''参数''|标题  =  &lt;caption _参数_&gt;Caption

</pre>
**注意**：　灵活运用colspan和rowspan参数来控制表格