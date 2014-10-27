title: 启动系统遇到grub rescue提示怎么办？
date: 2014-10-26 19:59:03
categories:
- IT
tags:
- linux
- ubuntu
- os
---
今天不清楚具体什么原因，可能是不知不觉对系统做了一些更改，导致系统无法正常重启了，具体表现为屏幕上提示错误

{% code %}
error: file '...' not found
Entering rescue mode...
grub rescue>
{% endcode %}

网上有很多针对这个问题的解决方式，但我尝试了之后都不行。后来问题解决之后，再来回想一下，才知道是因为自己没有理解这一系列步骤的含义，盲目机械地照着输入。很多时候盲目地输入也许运气好就能解决问题，但也有一些时候，因为网上的文章所针对的问题的背景也许和你当前遇到的情况不是完全相同，机械地对照着做也就不一定生效了。

对于这个问题，首先，输入ls命令
{% code %}
grub rescue>ls
(hd0) (hd0,msdos10) (hd0,msdos9) (hd0,msdos6) (hd0,msdos5) (hd0,msdos4) ...
{% endcode %}

每个结果都是一个分区，ls一下就能显示该分区下的文件，接下来这个步骤其实就是为了找一个叫grub的文件夹，但在之前我没有意识到这个本质，照着网上的说法

ls (hd0,msdosX)/boot/grub

依次在每个分区下找，但是找了几遍都没找到。后来我才意识到我曾经给/boot单独弄了个分区，而不是默认把所有路径都装在一个分区里，所以实际上我应该执行

ls (hd0,msdosX)/grub

来寻找（grub一般是boot下的一个文件夹）

找到grub文件夹之后那剩下的问题就简单了

{% code %}
grub rescue>set root=AAA
grub rescue>set prefix=BBB
grub rescue>insmod normal
grub rescue>normal
{% endcode %}

AAA为grub所在的那个分区名，类似(hd0,msdosX)的形式
BBB为grub的具体路径，类似(hd0,msdosX)/XXX/YYY/ZZZ/grub

如果一切顺利，当你执行normal这个指令之后，就会出现平时正常开机时那个熟悉的紫色界面了。


