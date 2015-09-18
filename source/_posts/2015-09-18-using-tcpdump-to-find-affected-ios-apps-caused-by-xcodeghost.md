title: 使用tcpdump来发现受Xcode Ghost感染的App
date: 2015-09-18 21:58:16
categories:
- IT
tags:
- security
- tcpdump
- xcodeghost
---
昨天一条微博引发了国内安全界的巨震，微博里透露说对国内若干个知名的IOS App的网络流量抓包的时候发现了一个不明网址，这些App会向这个叫init.icloud-analysis.com的不明网址发送当前App的一些信息，并且有潜在地可能钓鱼窃取用户的iCloud账号密码。

而这些App有个共同点是都使用了一份被修改并注入恶意代码的Xcode开发工具所开发，这些被注入的Xcode的安装包并非是由官网下载，而是通过百度云盘或迅雷下载而来。因为官网源在国内的下载速度非常慢，所以很多开发者，甚至是这些大公司的开发者们也会去国内的这些网盘下载Xcode安装包。更多具体信息大家去网上一搜就是一大把，我这就不多说了。

那我要在这篇文章里说的就是怎么去抓包检测你当前的手机里的App是否“中毒”了。用什么工具呢？其实就是之前在我的博文里提到的tcpdump。主要的思路就是自建wifi网络，用手机去连这个自建的网络，然后在电脑里抓包分析某个App是否有向那个不明网站发送数据。

如何在mac osx上自建wifi网络？具体的我也不说了，大致的做法就是先在右上角的wifi图标点击后的下拉列表下选择 Create Network，然后在 System Preferences > Sharing 下的 Internet Sharing 里选择将 Thunderbolt Bridge共享，下面的选项里选wifi，也就是说将你电脑的有线网络分享给连接wifi的设备。

然后用手机连上刚才创建的wifi，先把所有app进程杀掉。

在命令行里输入

{% code lang:bash %}
sudo tcpdump -i any -lvvvA port 53 | grep icloud-analysis
{% endcode %}

这个时候，就可以开始在手机上测试App了，比如首先点开“网易云音乐”，不一会儿，你就会看到相关的请求数据包出现了

{% code lang:bash %}
    192.168.2.3.61037 > 192.168.2.1.domain: [udp sum ok] 44733+ A? init.icloud-analysis.com. (42)
..........m.5.2...............init.icloud-analysis.com.....
    192.168.2.3.61037 > 192.168.2.1.domain: [udp sum ok] 44733+ A? init.icloud-analysis.com. (42)
..........m.5.2...............init.icloud-analysis.com.....
    192.168.2.3.61037 > 192.168.2.1.domain: [udp sum ok] 44733+ A? init.icloud-analysis.com. (42)
b...	d.c.[1/..E..F....@............m.5.2...............init.icloud-analysis.com.....
    192.168.2.3.61037 > 192.168.2.1.domain: [udp sum ok] 44733+ A? init.icloud-analysis.com. (42)
b...	d.c.[1/..E..F....@............m.5.2...............init.icloud-analysis.com.....
    192.168.2.3.61037 > 192.168.2.1.domain: [udp sum ok] 44733+ A? init.icloud-analysis.com. (42)
b...	d.c.[1/..E..F`n..@............m.5.2...............init.icloud-analysis.com.....
    192.168.2.3.61037 > 192.168.2.1.domain: [udp sum ok] 44733+ A? init.icloud-analysis.com. (42)
b...	d.c.[1/..E..F`n..@............m.5.2...............init.icloud-analysis.com.....
{% endcode %}

这些其实是init.icloud-analysis.com的DNS域名解析请求，有域名解析请求意味着代码中含有对改地址的请求。

打开一个App，如果有类似上面的请求，说明中招了，没有的话则没问题。测完一个之后，杀掉进程，再开启另外一个App测。
