---
title: Transmission服务器端搭建
date: 2021-01-13 15:23:46
categories:
  - 技术
tags:
  - 闲的蛋疼
  - 浪费时间
  - 不务正业
---

我把闲置的服务器用来做种了，做种的文件放在另一块硬盘中，远程用浏览器监控Transmission的运行情况。

所以需要折腾的技术有以下几部分：

- 把硬盘格式化，自动挂载，修改权限
- 更改Transmission的设置，把负责做种的账户改成自己

- 更改Transmission的设置，允许远程访问。

改Transmission的设置遇到的坑我真是数都数不过来。。。

<!--more-->

## 硬盘挂载

使用`df`和`du`两个指令查看磁盘的情况，可以查看RetHat官方的[文章](https://www.redhat.com/sysadmin/du-vs-df)（复习的时候看到RetHat的商业模式是免费试用，付费咨询，老增值模式了）。

简单来说`df`是`disk free`，查看磁盘的**大小**，**使用**和**剩余**。通常使用`df -h`，可以把字节以”方便人类阅读“的形式展现。

`du`是`disk usage`，更多用来查看子目录的使用情况。

当然，这是针对挂载了的硬盘而言的，我就是查看之后发现我的硬盘容量岌岌可危才打算在机械硬盘上做种的。

用`fdisk`指令查看所有的磁盘情况。

```bash
sudo fdisk -l
```

我的服务器上有两块空闲的磁盘

![image-20210113153823441](https://franktian-blog.oss-cn-beijing.aliyuncs.com/img/image-20210113153823441.png)

一块固态，一块机械。

接下来对硬盘进行分区

```bash
sudo fdisk /dev/sda
```

进入硬盘管理模式后，输入`n`创建新的分区，然后跟着提示走就行。

提示会以此让你选

- Partition type
- Partition number
- Frist sector
- Last sector

最后输入`w`保存设置。

注意，如果Partition type不能从0开始选，或者Partition number不能从2048开始选，说明你的磁盘可能有其他区。

如果想删除其他分区，同样使用`fdisk`，然后输入`d`，之后跟着提示走就行。

分区好了之后要格式化，格式化为`ext4`格式

```bash
sudo mkfs -t ext4 /dev/sda
```

最后新建个文件夹，挂载就行，比如我把机械硬盘挂载在`MHD`文件夹下

```bash
mkdir MDH
sudo mount /dev/sda ~/MHD
```

但是这样的缺点是每次开机都要重新手动挂载，我们可以让机器启动时自动挂载。

用`blkid`获取磁盘的uuid和属性

```bash
sudo blkid
```

找到你要挂载的，记住它的uuid，在`/etc/fstab`添上一行自动挂载的语句

```bash
sudo vim /etc/fstab
```

模版是

```bash
UUID={你的硬盘的uuid}  /u01  ext4  defaults  1  1
```

别忘了设着硬盘的所有者为你

```bash
sudo chown -R frank:frank ~/MHD
```

## Transmission用户设置

安装```transmission-daemon```，这玩意是transmission的守护进程，它会帮你下载```transmission-cli```什么的。不过我们配置好了就不需要用命令行了（用命令行做种也太古早了）

```bash
sudo apt install transmission-daemon
```

下载好了它就会自动开启，这时候要先关闭它

```bash
sudo service transmission-daemon stop
```

把配置文件中的USER改成你自己

```bash
sudo vim /etc/init.d/transmission-daemon
```

下面的也是，把USER改成你自己

```bash
sudo vim /lib/systemd/system/transmission-daemon.service
```

最后给配置文件的用户改成你自己

```bash
sudo chown -R frank:frank /etc/transmission-daemon
sudo chown -R frank:frank /var/lib/transmission-daemon/
```

现在还不能启动，要再把网络权限改一下

## Transmission远程管理设置

打开`/etc/transmission-daemon/settings.json`

要更改的是下面的内容

- rpc-username改成远程登录想用的用户名
- rpc-password改成远程登录想用的密码
- rpc-whitelist改成"\*.\*.\*.\*"，或者把rpc-whitelist-enabled改成false

这个还有一个坑，似乎是自动挂载的问题，就是它开始可能不会读这个文件的内容，还要改一下`~/.config/transmission-daemon/settings.json`的内容，把这个cp过去就好了。

注意这个文件的拥有者也要是你自己。

最后，介绍一个很好用的web界面，仓库地址是https://github.com/ronggang/transmission-web-control。

下载releases的版本之后安装就好了。

## Enjoy!

打开守护进程

```bash
sudo service transmission-daemon start
```

在自己的电脑上打开网页`{服务器的ip}:9091`

Enjoy it!



