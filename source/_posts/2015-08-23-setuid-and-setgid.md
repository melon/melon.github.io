title: chmod之setuid和setgid
date: 2015-08-23 16:36:55
categories:
- IT
tags:
- linux
- chmod
- setuid
- setgid
---
你可能见过下面这句命令

{% code lang:bash %}
chmod 6555 htop
{% endcode %}

与通常chmod带上三个数字的用法相比，这句多了个数字。

其实三个数字的形式默认是省略了第一个数字0

{% code lang:bash %}
chmod 644 htop
# is the same as
chmod 0644 htop
{% endcode %}

这四个数字中的第一位是用来设置setuid、setgid以及sticky bit用的

setuid是针对owner的，setgid是针对group member的，当赋予了setuid和setgid后，文件或文件夹相应掩码的x位就会变成s

{% code lang:bash %}
-r-sr-sr-x  1 root  wheel  1111 Jan 24 2013 mtr
{% endcode %}

setuid和setgid被用于文件和文件夹时有着完全不同的含义

### 作用于文件时
对于文件来说，setuid和setgid只会对可执行文件起效果。它们的作用是，当可执行文件执行时，自动将该文件的owner和group的角色赋予给当前的操作者，也即当前的操作者自动拥有了该文件的owner权限和group权限，而无需关心当前操作者自己的owner角色和所属group。

比如有如下文件，不管是哪个用户去执行它，都是以root:wheel的身份去执行的

{% code lang:bash %}
-r-sr-sr--  1 root  wheel  1111 Jan 24 2013 start.sh
{% endcode %}

### 作用于文件夹时
当setuid设置在文件夹上时，大多linux版本时无视的，所以一般来说，setgid作用于文件夹的情况比较多。当给某一文件夹设置setgid时，所有在该文件夹下创建的文件或文件夹将自动继承这个父文件夹的group角色。这个功能在组内成员协作的时候会比较有用，例如我在之前的一片博文[在服务器上部署git仓库时的要点剖析](/blog/2014/10/27/key-point-of-getting-git-on-server/)里提到过setgid的应用。

- References:
  1. [setuid](https://en.wikipedia.org/wiki/Setuid)
