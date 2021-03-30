---
title: 在 CentOS7 上安装 MongoDB
date: 2017-08-09 15:07:58
tags:
  - MongoDB
  - CentOS
---

[原文地址](http://www.linuxidc.com/Linux/2016-06/132675.htm)

#### 1 通过 SecureCRT 连接至 CentOS7 服务器；

#### 2 进入到 /usr/local/ 目录：

```bash
cd /usr/local
```

#### 3 在当前目录下创建 tools 目录：

```bash
mkdir -p tools
```

#### 4 进入到 tools 目录中：

```bash
cd tools
```

#### 5 下载与 CentOS 系统匹配的 mongodb-linux-x86_64-rhel70-3.2.4.tgz 文件：

```bash
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.2.4.tgz
```

#### 6 解压缩 mongodb-linux-x86_64-rhel70-3.2.4.tgz 文件：

```bash
tar -zxvf mongodb-linux-x86_64-rhel70-3.2.4.tgz
```

解压完成后得到 mongodb-linux-x86_64-rhel70-3.2.4 文件夹及其所属的文件；

#### 7 重命名 mongodb-linux-x86_64-rhel70-3.2.4 文件为 mongodb3.2.4：

```bash
mv mongodb-linux-x86_64-rhel70-3.2.4 mongodb3.2.4
```

#### 8 返回到上一级目录，并创建 mongodb 目录：

```bash
cd ../mkdir -p mongodb
```

#### 9 将 mongodb3.2.4 文件从`/usr/local/tools`目录中移动到 /usr/local/mongodb 目录中：

```bash
mv tools/mongodb3.2.4/ mongodb/
```

#### 10 进入到`/usr/local/mongodb/mongodb3.2.4`目录中：

```bash
cd mongodb/mongodb3.2.4
```

#### 11 在`/usr/local/mongodb/mongodb3.2.4/bin/`目录中创建一个存放日志的目录：

```bash
mkdir -p data/test/logs
```

#### 12 在`/usr/local/mongodb/mongodb3.2.4/bin/`目录中创建一个存放数据文件的目录：

```bash
mkdir -p data/test/db
```

#### 13 进入 bin 目录中：

```bash
cd bin/
```

#### 14 创建配置文件 mongodb.conf：

```bash
vi mongodb.conf
```

在 mongodb.conf 写入如下内容：

```bash
# idae - MongoDB config start - 2016-05-02

# 设置数据文件的存放目录
dbpath = /usr/local/mongodb/mongodb3.2.4/bin/data/test/db

# 设置日志文件的存放目录及其日志文件名
logpath = /usr/local/mongodb/mongodb3.2.4/bin/data/test/logs/mongodb.log

# 设置端口号（默认的端口号是 27017）
port = 27017

# 设置为以守护进程的方式运行，即在后台运行
fork = true

# nohttpinterface = true
nohttpinterface = true
# idae - MongoDB config end - 2016-05-02
```

按英文状态下的“:wq”保存并退出；

参数解释:

> --dbpath 数据库路径(数据文件)

> --logpath 日志文件路径

> --master 指定为主机器

> --slave 指定为从机器

> --source 指定主机器的 IP 地址

> --pologSize 指定日志文件大小不超过 64M.因为 resync 是非常操作量大且耗时，最好通过设置一个足够大的 oplogSize 来避免 resync(默认的 oplog 大小是空闲磁盘大小的 5%)。

> --logappend 日志文件末尾添加，即使用追加的方式写日志

> --journal 启用日志

> --port 启用端口号

> --fork 在后台运行

> --only 指定只复制哪一个数据库

> --slavedelay 指从复制检测的时间间隔

> --auth 是否需要验证权限登录(用户名和密码)

> --syncdelay 数据写入硬盘的时间（秒），0 是不等待，直接写入

> --notablescan 不允许表扫描

> --maxConns 最大的并发连接数，默认 2000

> --pidfilepath 指定进程文件，不指定则不产生进程文件

> --bind_ip 绑定 IP，绑定后只能绑定的 IP 访问服务

#### 15 启动 mongodb 服务：

##### 15.1 以自定义的 mongodb 配置文件方式启动：

```bash
./mongod --config mongodb.conf
```

##### 15.2 以修复模式启动 mongodb：

```bash
./mongod --repair -f mongodb.conf
```

##### 15.3 以参数式启动：

```bash
./mongod /usr/local/mongodb/mongodb3.2.4/bin/mongod --dbpath=/usr/local/mongodb/mongodb3.2.4/bin/data/test/db --logpath=/usr/local/mongodb/mongodb3.2.4/bin/data/test/logs/mongodb.log --fork
```

如果报如下错误：

```bash
ERROR: child process failed, exited with error number 1
```

很可能是 mongodb.conf 中配置的路径不一致问题；

如果报如下错误：

```bash
ERROR: child process failed, exited with error number 100
```

很可能是没有正常关闭导致的，那么可以删除 mongod.lock 文件

#### 16 查看 mongodb 进程：

```bash
ps aux |grep mongodb
```

#### 17 查看 mongodb 服务的运行日志：

```bash
tail -200f /usr/local/mongodb/mongodb3.2.4/bin/data/test/logs/mongodb.log
```

#### 18 检查端口是否已被启动：

```bash
netstat -lanp | grep 27017
```

#### 19 杀死 mongodb 进程，即可关闭 mongodb 服务：

```bash
kill -15 PID
```

PID 可以通过步骤 16 查看到

#### 20 如果出现如下情况

```bash
warning: Failed to connect to 127.0.0.1:27017, reason: errno:111 Connection refused
```

可以采用如下两种方式解决：

##### 20.1 若数据库出现如上不能连接的原因，可能是 data 目录下的 mongod.lock 文件问题，可以用如下命令修复：

```bash
./bin/mongod --repair
```

##### 20.2 或者直接删除 mongod.lock

```bash
rm -f /usr/local/mongodb/data/db/mongod.lock
```

然后再启动 mongodb 服务：

```bash
./mongod --config mongodb.conf
```

如果以上两部依然解决不掉，则是路径文件，我们可以删除`/usr/local/mongodb/mongodb3.2.4/bin/data` 目录及其子目录，并采用绝对路径的方式：

```bash
./mongod /usr/local/mongodb/mongodb3.2.4/bin/mongod --dbpath=/usr/local/mongodb/mongodb3.2.4/bin/data/test/db --logpath=/usr/local/mongodb/mongodb3.2.4/bin/data/test/logs/mongodb.log --fork
```

#### 21 将 mongodb 服务加入到自启动文件中：

```bash
vi /etc/rc.local
```

在文件末尾追加如下命令：

```bash
/usr/local/mongodb/mongodb3.2.4/bin/mongod --config mongodb.conf
```

保存并退出：

```bash
:wq!
```

#### 22 客户端常用命令：

```bash
db.version(); # 查看 mongodb 的版本

use test; # 进入到指定的数据中，这里是 test 数据库
```

#### 23 在`/usr/local/mongodb/mongodb3.2.4/bin/`目录中，键入如下命令，打开一个 mongodb 的客户端程序，即打开一个 mongodb 的 shell 客户端，这个 shell 客户端同时也是一个 JavaScript 编辑器，即可用输入任何的 JavaScript 脚本：

```bash
./mongo
```

默认连接至 test 数据库

#### 24 在浏览器中输入 IP:27017，如：[http://101.201.212.129:27017/](http://101.201.212.129:27017/) 可以查看 MongoDB 的版本信息；
