title: "如何将linode VPS上的ext3文件系统改为ext4"
date: 2014-09-23 23:25
categories:
- IT
tags:
- linux
---
把系统文件系统从ext3升级到ext4这个需求其实来源于最近对mongodb的一点研究，在这个[Production Notes](http://docs.mongodb.org/manual/administration/production-notes/)里提到了，使用mongodb数据库时是推荐使用ext4文件系统的。查询相关资料之后不难发现，相比于ext3，ext4增加了许多特性，这些特性能为系统提供更加的性能和可靠性。

然而，linode的系统的最近一次大升级之前，默认安装的最新文件系统只支持到了ext3（虽然现在linode平台已经支持安装时选择ext4文件格式），我就是一年之前平台未升级时开始使用linode的，那个时候安装系统还是采用了ext3格式。因为mongodb这个原因，我希望将系统文件格式升级到ext4，并且想要在保留数据的情况下直接升级。于是我便去查询了相关资料，基本上完美地成功升级。

升级之前为了保证数据安全，请务必备份系统！

首先确保你的Linux内核版本高于2.6.30版本，再低的版本可能会有些问题。然后按照下面的步骤：

1.登录你的Linode管理页面[Linode Manager](https://manager.linode.com)，在你希望升级的那台主机的Rescue项目下，点击Reboot into Rescue Mode，重启进入安全模式。

2.在Remote Access下，通过点击Launch Lish Ajax Console，进行网页登录终端。

3.在终端下先执行
{% code lang:bash %}
apt-update
apt-get install debian-archive-keyring e2fsprogs
{% endcode %}
将所需的软件升级到最新版本，以便支持ext4更新

4.进行ext4转换，依次执行以下两个命令
{% code lang:bash %}
tune2fs -O extents,uninit_bg,dir_index /dev/xvda
e2fsck -fDC0 /dev/xvda
{% endcode %}
/dev/xvda是希望升级的设备，待命令执行完之后，系统就已经升级到ext4了

5.重启系统，然后进入系统后就会以ext4方式加载分区了。

登录系统之后用df -T命令查看就可以发现文件系统已经变成ext4了。

值得一提的是，这个方法有个小小的瑕疵就是，这样升级之后，Linode Manager中并没有识别到系统已经采用了ext4文件格式，还是展现成ext3格式。但是这个问题其实无伤大雅，忽略它就好了。

- References:
	1. [Upgrading the root file system on a Linode to ext4](http://miknight.blogspot.jp/2010/11/upgrading-root-file-system-on-linode-to.html)