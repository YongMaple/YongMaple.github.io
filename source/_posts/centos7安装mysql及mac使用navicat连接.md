---
title: centos7安装mysql及mac使用navicat连接
date: 2017-08-29 15:07:58
categories: 工具
tags:
  - mysql
  - CentOS
  - navicat
---

### 安装 mysql

安装部分原文地址：[http://www.linuxidc.com/Linux/2016-09/135288.htm](http://www.linuxidc.com/Linux/2016-09/135288.htm)

#### 配置 YUM 源

在 MySQL 官网中下载 YUM 源 rpm 安装包：[http://dev.mysql.com/downloads/repo/yum/](http://dev.mysql.com/downloads/repo/yum/)

##### 下载 mysql 源安装包

```shell
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

```shell

yum localinstall mysql57-community-release-el7-11.noarch.rpm
```

##### 检查 mysql 源是否安装成功

```shell
yum repolist enabled | grep "mysql.*-community.*"
```

![](http://www.linuxidc.com/upload/2016_09/160918124758192.png)
看到上图所示表示安装成功。

可以修改 vim /etc/yum.repos.d/mysql-community.repo 源，改变默认安装的 mysql 版本。比如要安装 5.6 版本，将 5.7 源的 enabled=1 改成 enabled=0。然后再将 5.6 源的 enabled=0 改成 enabled=1 即可。改完之后的效果如下所示：

![](http://www.linuxidc.com/upload/2016_09/160918124758197.jpg)

#### 安装 MySQL

```shell
yum install mysql-community-server
```

#### 启动 MySQL 服务

```shell
systemctl start mysqld
```

查看 MySQL 的启动状态

```shell
systemctl status mysqld
```

显示如下

```shell
mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; disabled; vendor preset: disabled)
   Active: active (running) since 五 2016-06-24 04:37:37 CST; 35min ago
 Main PID: 2888 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─2888 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

6月 24 04:37:36 localhost.localdomain systemd[1]: Starting MySQL Server...
6月 24 04:37:37 localhost.localdomain systemd[1]: Started MySQL Server.
```

#### 开机启动

```shell
systemctl enable mysqld
```

```shell
systemctl daemon-reload
```

#### 修改 root 本地登录密码

mysql 安装完成之后，在/var/log/mysqld.log 文件中给 root 生成了一个默认密码。通过下面的方式找到 root 默认密码，然后登录 mysql 进行修改

```shell
grep 'temporary password' /var/log/mysqld.log
```

![](http://www.linuxidc.com/upload/2016_09/160918124758193.png)

```shell
mysql -uroot -p
```

```mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

或者

```mysql
mysql> set password for 'root'@'localhost'=password('MyNewPass4!');
```

注意：mysql5.7 默认安装了密码安全检查插件（validate_password），默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于 8 位。否则会提示 ERROR 1819 (HY000): Your password does not satisfy the current policy requirements 错误，如下图所示：

![](http://www.linuxidc.com/upload/2016_09/160918124758194.png)

通过 msyql 环境变量可以查看密码策略的相关信息：

```mysql
mysql> show variables like '%password%';
```

![](http://www.linuxidc.com/upload/2016_09/160918124758195.png)

validate_password_policy：密码策略，默认为 MEDIUM 策略  
validate_password_dictionary_file：密码策略文件，策略为 STRONG 才需要  
validate_password_length：密码最少长度  
validate_password_mixed_case_count：大小写字符长度，至少 1 个  
validate_password_number_count ：数字至少 1 个  
validate_password_special_char_count：特殊字符至少 1 个  
上述参数是默认策略 MEDIUM 的密码检查规则。

共有以下几种密码策略：

| 策略               | 检查规则                                                              |
| ------------------ | --------------------------------------------------------------------- |
| 0 or LOW           | Length                                                                |
| 1 or MEDIUM Length | numeric, lowercase/uppercase, and special characters                  |
| 2 or STRONG Length | numeric, lowercase/uppercase, and special characters; dictionary file |

MySQL 官网密码策略详细说明：http://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html#sysvar_validate_password_policy

##### 修改密码策略

在/etc/my.cnf 文件添加 validate_password_policy 配置，指定密码策略

选择 0（LOW），1（MEDIUM），2（STRONG）其中一种，选择 2 需要提供密码字典文件

`validate_password_policy=0`

如果不需要密码策略，添加 my.cnf 文件中添加如下配置禁用即可：

`validate_password = off`

重新启动 mysql 服务使配置生效：

```shell
systemctl restart mysqld
```

#### 添加远程登录用户

默认只允许 root 帐户在本地登录，如果要在其它机器上连接 mysql，必须修改 root 允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，我添加一个新的帐户：

```mysql
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

#### 配置默认编码为 utf8

修改/etc/my.cnf 配置文件，在[mysqld]下添加编码配置，如下所示：

```txt
[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
```

重新启动 mysql 服务，查看数据库默认编码如下所示：

```mysql
mysql> show variables like '%character%';
```

![](http://www.linuxidc.com/upload/2016_09/160918124758196.png)

默认配置文件路径：  
配置文件：/etc/my.cnf  
日志文件：/var/log//var/log/mysqld.log  
服务启动脚本：/usr/lib/systemd/system/mysqld.service  
socket 文件：/var/run/mysqld/mysqld.pid

### navicat 连接

在使用 navicat 连接时，一直报错

```
2013 - Lost connection to MySQL server at 'reading
authorization packet', system error: 54 "Connection reset by peer"
```

但是通过命令行是可以连接的

最后发现需要在 navicat 中配置 ssh 通道，此处的用户名密码是 centos 服务器的用户名和密码
