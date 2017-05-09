---
title: linux环境折腾
date: 2017-05-09 09:46:26
tags: linux
---

本文章主要记录linux如何变为工作环境，区别于Linux命令手册文章

### 发行版
工作环境暂时选择`deepin`，原因如下:
- 工作环境离不开qq，ubuntu qq巨卡。基本只有deepin了。
- 开发比较活跃，有bug随时提交
- 觉得不过瘾可以自己修改bug
- 有需求随时提交
- 觉得不过瘾可以自己修改代码然后`pull request`
- 开发者中国人，交流方便

缺点:
- 稳定性有待考察

心得:
- 深度终端不错，颜值高，实用。
- 终端雷神模式不错。`alt + F2`打开隐藏，使用方便。
- 基本满足工作环境
	- **即时通信** - QQ 跑起来虽然还是很耗资源，但是问题不大
	- **email** - ThunderBird/Mutt 正在折腾中
	- **linux开发环境** - 和工作开发环境一样，不用和以前一样跑虚拟机了。

<!--more-->

### 编辑器
#### 键盘映射
deepin 上替换`esc`和`caps lock`因为`acps locl`基本没什么用还占地方
```bash
gsettings set com.deepin.dde.keybinding.mediakey capslock '[]'
gsettings set com.deepin.dde.keyboard layout-options '["caps:swapescape"]'
```
#### VIM主题
暂时使用`oh-my-zsh`适不适合开发有待考察。

### 终端
深度的终端做的不错，颜值很高。
__强烈推荐`alt + f2`雷神模式__

不喜欢bash，装个zsh吧。

#### zsh主题
可以选择random 然后看到自己喜欢的在设置
1. `agnoster`
2. `sonicradish`

#### zsh插件
* `autojump` 自动跳转目录的工具，配合zsh使用
* `zsh-autosuggestions`  自动命令补全
* `asciinema` 录制文本终端

### mail
在win下面，习惯用foxmail。Linux下面用下面这两个把
- ThunderBird *图像界面*
	1. 安装MinimizeToTray revived (MinTrayR)插件，安装完后需要设置一下就可以让Thunderbird支持后台运行。
- Mutt *终端* 
	1. [比较友好的文档 - *来自arch wiki*](https://wiki.archlinux.org/index.php/mutt) 
	1. __安装:__ 用 aptitude 安装即可 
	2. __配置:__
		1. 新建配置文件: `~/.muttrc`或者`~/.mutt/muttrc`，安装完后没有这两个文件，我自己新建了一个。如下配置可以正常收取邮件。

```bash
set imap_user   = 791628659
set imap_pass   = ****************
set folder      = imaps://imap.qq.com/
set spoolfile   = +INBOX
#mailboxes       = +INBOX
#set imap_check_subscribed
#
#set header_cache = ~/.cache/mutt
#set message_cachedir = "~/.cache/mutt"

#unset imap_passive
#set imap_keepalive = 300
# How often to check for new mail (time in seconds).
#set mail_check = 120

set my_name = "Breaker"
```
### 网络配置
* [linux网络配置](https://wiki.deepin.org/index.php?title=%E7%BD%91%E7%BB%9C%E7%AE%A1%E7%90%86)
### deepin
* [deepin自启动](https://wiki.deepin.org/index.php?title=%E8%87%AA%E5%90%AF%E5%8A%A8%E7%A8%8B%E5%BA%8F)

### 微信
```
https://github.com/geeeeeeeeek/electronic-wechat
npm config set registry https://registry.npm.taobao.org
npm config get registry
```
添加一个软连接到/usr/bin即可
在/usr/share/applications中添加wechat.desktop 

2017-05-09 09:45:08
最新的deepin上已经集成了lectronic-wechat

### 其他小工具
- workrave 定时休息，deepin上运行良好
