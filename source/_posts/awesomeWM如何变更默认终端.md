---
title: awesomeWM如何变更默认终端
thumbnail: 'https://duangsuse.gitlab.io/blog/img/thumb/awesome.png'
date: 2017-07-14 11:13:11
categories: 操作系统管理
tags: gnu/linux 窗口管理器 轻量 GUI
---
偶然的机会折腾上了awesomeWM,发现改变默认终端需要额外下功夫,便发一篇博文明说

### 安装awesome
推荐_不使用_DM(显示管理器),现在的大部分DM都比较臃肿(如GDM,SSDM),建议在配置好的系统上直接`pacman -S awesome`来执行安装,然后可以执行`echo "awesome">$HOME/.xinitrc`来设置awesome为您的窗口管理器.
### 启动awesome
当做好以上工作时,就可以启动awesome了,可以随便挑一个tty执行`startx`来启动awesomeWM.
__注意:X服务器会占用你选择的tty作为图形控制台,直到服务结束__
### 变更默认终端
你会发现awesome默认的terminal是_xterm_.xterm是一个很~~旧~~经典的终端了,有可能会`缺失许多字体`,或者是`字体渲染`~~渣成什么~~`不好`,所以你可能想用其它终端替换它.我们可以看看怎么`修改awesome的配置文件`.
先试试菜单,就是左上的那个图标,点击一下,或者使用快捷键`Super+W`,选择子菜单`awesome`,你会看到里面有一个`edit config`,执行它.
>如果你因为没有看完本文就开心地把rc.lua里的terminal值改为你想要终端的二进制文件名称的话(比如_xfce4-terminal_),__可能导致以后菜单里除了打开终端本身以外需要打开terminal的项无法使用__,这样的话,可以 nano \`pacman -Ql awesome|grep awesome/rc.lua>/tmp/aweterm&&awk -e '{print $2}' '/tmp/aweterm'\`,在文本编辑器里把`^W(ctrl+W)`把terminal改回xterm...

等它打开了配置以后,先不慌动,再开一个terminal,__以root权限__执行这些命令:
```shell
touch /usr/bin/aweterm&&chmod +x /usr/bin/aweterm
nano /usr/bin/aweterm
```
在nano里,我们可以输入这些(当然你自己实现一个更好,或者拿其它语言写~~(最好不是python或Javascript)~~都可以,主要是翻译为你的虚拟终端能接受的参数格式)
```shell
#!/bin/sh
input=$@
echo ${input} > /tmp/aweterm
#trimed=`awk '{print $1 " " $2 " " $3 " " $4 " " $5}' /tmp/aweterm`
trimed=`cat /tmp/aweterm`
if [ $trimed=="" ] ;then
	xfce4-terminal&
else
	xfce4-terminal -x $trimed&
fi
exit
```
保存,然后你可以测试下`aweterm man awesome`和`aweterm`两个命令.它们应该能正常运行一个终端或打开了awesome的手册的终端,并且如果你看看执行的进程,那里应该_没有_`sh /usr/bin/aweterm`的字样.

然后就是改rc.lua了,在之前我们打开的窗口上按下`Ctrl+W`,输入__terminal=xterm__,回车,然后`可以将xterm改为aweterm`.
注:如果想要改变主题的话,可以先看看`/usr/share/awesome/themes`下的目录,在配置文件里^W找`default/theme.lua`,找到后可以随便将default改为之前找到的目录名


