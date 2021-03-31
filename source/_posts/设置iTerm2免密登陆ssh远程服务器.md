---
title: 设置iTerm2免密登陆ssh远程服务器
date: 2021-03-27 15:51:52
categories: 工具
tags:
  - iTerm2
---
转载自[https://zhuanlan.zhihu.com/p/180500618](https://zhuanlan.zhihu.com/p/180500618)

原文中代码存在bug，已修改，如下：
```sh
#!/usr/bin/expect -f
#trap sigwinch spawned
  trap {
    set rows [stty rows]
    set cols [stty columns]
    stty rows $rows columns $cols < $spawn_out(slave,name)
   } WINCH

set user username
set host 0.0.0.0
set password keys
set timeout -1
spawn ssh -XY $user@$host
expect "*password:*"
send "$password\r"
interact
expect eof
```

简述下逻辑就是，
1. 在本地你想存在命令的地方建个文件夹
2. 把上面的代码写好，保存进去
3. 然后在iterm2里配置好
4. 之后iterm2就可以一键执行代码了，用来登录服务器，并窗口可以自适应
具体可以看原文