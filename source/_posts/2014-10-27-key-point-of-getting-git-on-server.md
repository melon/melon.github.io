title: 在服务器上部署git仓库时的要点剖析
date: 2014-10-27 13:00:45
categories:
- IT
tags:
- git
---
每次想在服务器上部署git仓库的时候，都忘记了步骤，每每比求助于这个[页面](http://git-scm.com/book/en/v2/Git-on-the-Server-Getting-Git-on-a-Server)，我想我有必要对一些关键点进行剖析，以帮助记忆。

### I

首先第一步是从本地仓库clone出一个副本来

{% code lang:bash %}
git clone --bare my_project my_project.git
{% endcode %}

与通常的clone不同，这里加了个**--bare**属性。实际上，对比之后你会发现，这个clone出来的my_project.git中的内容和原来的my_project中的隐藏文件夹.git下的内容相差无几，上述页面中也说了用复制.git隐藏文件夹的方式能获得和上述clone --bare的操作几乎一样的效果，两者都是不包含了working directory的文件夹。

那两者的微小差异到底在哪儿？

我尝试粗略对比了一下，发现了clone --bare出来的版本的变化：
1.config文件中的内容有一些小变化，关于branch的属性都没了
2.index文件和logs/文件夹都消失了
3.packed-refs文件大小有些变化

可能还有一些变动我并未发现，但我想这些变动的目的可能都是为了精简内容，剔除一些服务器上的版本所不需要的内容。

### II

接下来第二步就是把这个clone出来的文件夹上传到服务器了，这部分没什么疑问，不赘述。

### III

第三步是权限相关的内容，这部分我感觉其实超越了git本身的设置，本质上是linux下的权限设置问题了。

这一步是说当你把版本库上传到服务器之后，需要进入那个文件夹，执行一条指令

{% code lang:bash %}
git init --bare --shared
{% endcode %}

执行了这条指令之后，某些文件夹的组权限就会由rwx变成了rws，这里的s表示的是setgid，当一个文件夹拥有setgid属性时，不管是哪个用户在这个文件夹下创建文件，这个文件的组都会被设置成这个setuid文件夹的拥有者的组，而不是当前创建者的组。举个例子，假如某个文件夹的组为developer，这时有个用户user2，它属于组user2和developer，当它在这个文件夹下新建一个文件时，这个新文件的组不会被设置成user2，而是developer，即和文件夹的组保持一致。

### END

这回应该不会再忘记了吧？希望你们也都能记住～

- References:
    1. [4.2 Git on the Server - Getting Git on a Server](http://git-scm.com/book/en/v2/Git-on-the-Server-Getting-Git-on-a-Server)
