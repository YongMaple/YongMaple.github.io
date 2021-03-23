---
title: iTerm2配色修改
date: 2017-02-04 10:25:12
categories: 工具使用
tags:
    - 开发工具
    - iTerm2
---
1. 首先`sudo vim ~/.bash_profile`，输入密码后按e进入编辑，看别人的帖子是不需要sudo的。。。但是我需要
2. 然后i进入编辑模式，加入下面这段，然后`esc`输入`:wq`保存   
```vim
#enables colorin the terminal bash shell export
export CLICOLOR=1

#sets up thecolor scheme for list export
export LSCOLORS=gxfxcxdxbxegedabagacad

#sets up theprompt color (currently a green similar to linux terminal)
export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;36m\]\w\[\033[00m\]\$ '

#enables colorfor iTerm
export TERM=xterm-color
```
3. 然后修改`preference->profiles->Terminal->xterm-new`   
![](http://okmneu7zl.bkt.clouddn.com/WX20170204-095600.png)   
4. 去挑选喜欢的配色方案，我在这里找的[http://iterm2colorschemes.com/](http://iterm2colorschemes.com/)
5. 下载到你喜欢的配色后，导入   
![](http://okmneu7zl.bkt.clouddn.com/WX20170204-095639.png)   
导入后选择你的配色，然后注销mac，再打开就发现配色变了，之后再换配色只要直接改就行了
