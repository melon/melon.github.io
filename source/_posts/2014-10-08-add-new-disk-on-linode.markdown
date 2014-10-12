title: "在linode上添加新磁盘"
date: 2014-10-08 13:59
categories:
- IT
tags:
- linux
---
在linode上，有时候我们不希望一下子把所有套餐内分配的空间用掉，通常我们可以先开出一个一定容量的磁盘，剩余空间以后再新建一个磁盘来使用。

新的磁盘可以这样创建：在Linode的管理面板的Linodes>>linodexxxxxx下的Disk Images下列出了当前的所有磁盘，可以看到右下角有个Create a new Disk Image。点开后编辑好磁盘名、文件系统类型、磁盘大小，然后点保存，稍等片刻一个新的磁盘就创建好了。

为了使新磁盘能在原来的磁盘（系统盘）上能访问，我们首先要修改启动配置，编辑你当前的Configuration Profiles，在Block Device Assignment下将你刚才新添加的磁盘与某一设备名对应起来，这个设备名之后会用到，修改之后保存更改。然后重启系统。

重启后在/dev文件夹下就有你的新磁盘了，下面就讲加载（mounting）相关的内容。

### 创建Mount Point
所谓Mount Point，就是你希望进入新磁盘文件系统的根目录，一般在/mnt文件夹下创建一个文件夹。比如

{% code lang:bash %}
sudo mkdir /mnt/newdisk
{% endcode %}

### 手动加载
语法格式：

{% code lang:bash %}
sudo mount <device location> <mount point>
{% endcode %}

比如：

{% code lang:bash %}
sudo mount /dev/xvdb /mnt/newdisk
{% endcode %}

然后用df指令查看就能看到新的磁盘了

### 卸载（unmounting)

{% code lang:bash %}
sudo umount <device or mount point>
{% endcode %}
注意：是umount，不是unmount

### 自动加载
手动加载只是个临时措施，如果希望系统启动时自动帮你加载，需要编辑<strong>/etc/fstab</strong>文件

比如可以采用下面的例子,一般将options设置成defaults就行了，具体区别可以自行查阅更多资料。

{% code lang:bash %}
# /etc/fstab: static file system information.
#
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
proc        /proc        proc    defaults                       0 0
/dev/xvda   /            ext4    noatime,errors=remount-ro      0 1
/dev/xvdb   /mnt/newdisk ext4    defaults                       0 2
/dev/xvdc   none         swap    sw                             0 0
{% endcode %}

下次你重启系统的时候，系统就会帮你自动加载了。
