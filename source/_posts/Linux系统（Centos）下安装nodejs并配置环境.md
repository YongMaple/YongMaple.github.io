---
title: Linux系统（Centos）下安装nodejs并配置环境
date: 2017-08-30 15:07:22
categories: 工具
tags:
  - nodejs
  - CentOS
---

[原文地址](http://blog.csdn.net/qq_21794603/article/details/68067821)

总结 Centos 下安装 nodejs 并配置环境，记录成功安装的方法。推荐的安装方法是利用已编译的二进制文件安装，不推荐使用源码的形式安装，一是源码安装比较麻烦，二是需要自行下载编译浪费时间。

### 1.安装 nodejs

访问 nodejs 的官方网站的 downdolad，网址：[https://nodejs.org/en/download/](https://nodejs.org/en/download/)，可以看到最新的 nodejs 下载，包括不同的版本，截图如下所示。

![](http://img.blog.csdn.net/20170329190539370?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjE3OTQ2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

根据 Linux 的不同版本选择 32 位或 64 位，因为我的 linux 的虚拟机是 64 位的，所以我选择的是 64 位二进制安装文件（Linux Binariesx64),可以右键选择在新窗口中打开链接，记下这个地址。[https://nodejs.org/dist/v6.10.1/node-v6.10.1-linux-x64.tar.xz](https://nodejs.org/dist/v6.10.1/node-v6.10.1-linux-x64.tar.xz)

回到 linux 虚拟机，在控制台输入

```bash
# wget https://nodejs.org/dist/v6.10.1/node-v6.10.1-linux-x64.tar.xz
```

系统将会下载这个文件，可以选择下载到默认路径。成功下载，在命令行输入命令可以查看到文件。如下图所示。

![](http://img.blog.csdn.net/20170329194814703?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjE3OTQ2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

根据下载的文件可以看出它的压缩方式是.xz 的方式，所以不能直接使用 linux 命令 tar 直接下载。需要下载能够解压这种格式的工具。我们可以使用 yum 源来安装，控制台命令：

```bash
yum search xz
yum install xz.i386
xz -d node-v6.10.1-linux-x64.tar.xz
tar -xf node-v6.10.1-linux-x64.tar
mv node-v6.10.1-linux-x64 node-v6.10.1
```

在控制台输入`yum search xz`,在搜索到的列表中选择安装软件，如`yum install xz.i386`,解压完成后文件变为以.tar 后缀，使用`tar -xf node-v6.10.1-linux-x64.tar`解压文件。

可以更改文件名方便操作，在命令台输入`mv node-v6.10.1-linux-x64 node-v6.10.1`，更改文件名为 node-v6.10.1。

为了验证是否能够使用 node，我们可以输入`cd node-v6.10.1/bin`，输入`./node -v`查看 node 版本，安装成功则能够成功显示 node 版本。如下图所示。（截图中的 node -v 应该为./node -v，因为是后面截图的）。

![](http://img.blog.csdn.net/20170329193059346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjE3OTQ2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 2.配置 nodejs

要想 node 能够在全局能够使用，需要添加连接，在控制台输入下面命令能够实现。其中“/root/node-v6.10.1/bin/node”为二进制 nodejs 文件的目录，根据上面的步骤，我这里是直接解压到了 root 目录下，所以路径为/root/node-v6.10.1/bin/node。

```bash
ln -s /root/node-v6.10.1/bin/node /usr/local/bin/node
ln -s /root/node-v6.10.1/bin/npm /usr/local/bin/npm
```

配置完成后即可在任何目录下使用 node。

![](http://img.blog.csdn.net/20170329194133691?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjE3OTQ2MDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
