---
title: centos7配置防火墙，开启端口
date: 2017-08-10 15:07:58
tags:
  - CentOS
---

参考地址：

[ Centos7,配置防火墙，开启端口](http://blog.csdn.net/u013410747/article/details/61696178)

[Centos7（Firewall）防火墙开启常见端口命令](https://www.5yun.org/10074.html)

[firewall-cmd 的常用命令使用](http://www.mamicode.com/info-detail-1539878.html)

[CentOS 7 中 firewall-cmd 命令](http://blog.sina.com.cn/s/blog_43b39e250102v4zt.html)

---

### 1.centos7 版本对防火墙进行 加强,不再使用原来的 iptables,启用 firewall

#### 1.1 查看已开放的端口(默认不开放任何端口)

```bash
firewall-cmd --list-ports
```

#### 1.2 开启 80 端口

```bash
firewall-cmd --zone=public(作用域) --add-port=80/tcp(端口和访问类型) --permanent(永久生效)
```

#### 1.3 重启防火墙

```bash
firewall-cmd --reload
```

#### 1.4 停止防火墙

```bash
systemctl stop firewalld.service
```

#### 1.5 禁止防火墙开机启动

```bash
systemctl disable firewalld.service
```

#### 1.6 删除端口

```bash
firewall-cmd --zone= public --remove-port=80/tcp --permanent
```

### 2.centos7 以下版本

#### 2.1 开放 80，22，8080 端口

```bash
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
/sbin/iptables -I INPUT -p tcp --dport 22 -j ACCEPT
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
```

#### 2.2 保存

```bash
/etc/rc.d/init.d/iptables save
```

#### 2.3 查看打开的端口

```bash
/etc/init.d/iptables status
```

#### 2.4 关闭防火墙

##### 2.4.1 永久性生效，重启后不会复原

```bash
开启： chkconfig iptables on
关闭： chkconfig iptables off
```

##### 2.4.2 即时生效，重启后复原

```bash
开启： service iptables start
关闭： service iptables stop
```

### 3 常用命令

#### 3.1 安装 Firewall 命令

```bash
yum install firewalld firewalld-config
```

#### 3.2 Firewall 开启常见端口命令

```bash
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --zone=public --add-port=22/tcp --permanent
firewall-cmd --zone=public --add-port=21/tcp --permanent
firewall-cmd --zone=public --add-port=53/udp --permanent
```

#### 3.3 Firewall 关闭常见端口命令

```bash
firewall-cmd --zone=public --remove-port=80/tcp --permanent
firewall-cmd --zone=public --remove-port=443/tcp --permanent
firewall-cmd --zone=public --remove-port=22/tcp --permanent
firewall-cmd --zone=public --remove-port=21/tcp --permanent
firewall-cmd --zone=public --remove-port=53/udp --permanent
```

#### 3.4 批量添加区间端口

```bash
firewall-cmd --zone=public --add-port=4400-4600/udp --permanent
firewall-cmd --zone=public --add-port=4400-4600/tcp --permanent
```

#### 3.5 开启防火墙命令

```bash
systemctl start firewalld.service
```

#### 3.6 重启防火墙命令

```bash
firewall-cmd --reload  或者   service firewalld restart
```

#### 3.7 查看端口列表

```bash
firewall-cmd --permanent --list-port
```

#### 3.8 禁用防火墙

```bash
systemctl stop firewalld
```

#### 3.9 设置开机启动

```bash
systemctl enable firewalld
```

#### 3.10 停止并禁用开机启动

```bash
sytemctl disable firewalld
```

#### 3.11 查看状态

```bash
systemctl status firewalld或者 firewall-cmd --state
```

#### 3.12 永久开启 ftp 服务

暂时开启去除 --permanent

```bash
firewall-cmd --add-service=ftp --permanent
```

#### 3.13 永久关闭 ftp 服务

暂时关闭去除 --permanent

```bash
firewall-cmd --remove-service=ftp --permanent
```

#### 3.14 让设置生效

```bash
systemctl restart firewalld
```

#### 3.15 检查设置是否生效

```bash
iptables -L -n | grep 21
```

#### 3.16 查询服务启用状态

```bash
firewall-cmd --query-service ftp

firewall-cmd --query-service ssh

firewall-cmd --query-service samba

firewall-cmd --query-service http
```
