title: html的文档模式
date: 2015-01-21 16:13:45
categories:
- IT
- Notes
tags:
- html
- doctype
---
html中的文档模式是对历史遗留问题所妥协的产物。

在互联网早期，网页通常需要分别为Netscape Navigator浏览器和IE浏览器各自做一套，后来规范出来了之后，如果冒然立即采用新的规范，那么原来的那些按老模式写的页面会立即出现问题。浏览器厂商为了使遵循规范的新的网站和遵循老模式的旧网站能同时正常使用，从而推出了文档模式概念：Quirks Mode（怪异模式）和Standards Mode（标准模式）。

<!--more-->

现在是有三种文档模式了：Quirks Mode, Full Standards Mode, Almost Standards Mode。

### Quirks Mode

浏览器会去模拟Navigator4和IE5里的一些非标准的行为，从而保证那些老的网站能正常显示。当文档中没有DOCTYPE声明或有不正确的DOCTYPE声明时，浏览器就视为该模式。

然而，一切没那么简单，不同的浏览器采用了不同的Quirks Mode，真的是千奇百怪，详见引文2。

### Full Standards Mode

浏览器会尽可能地按照规范来展示网站。

但标准也在变，所以某个浏览器所谓的标准也许就是当它诞生时所能知晓的标准吧。

### Almost Standards Mode

浏览器只会去展示部分“怪异”行为。

更坑。

## 结论

最推荐的做法是，任何html的文档的第一句这么写

{% code lang:html %}
<!DOCTYPE html>
{% endcode %}

通常这么做就完美了，但是如果要应对IE系，你还要在head里添加

{% code lang:html %}
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
{% endcode %}

或者在服务器端返回一个头字段

{% code %}
X-UA-Compatible: IE=Edge
{% endcode %}

好，如果你不想深究，了解到这里就够了。


- References:
	1. [Quirks Mode and Standards Mode](https://developer.mozilla.org/en-US/docs/Quirks_Mode_and_Standards_Mode)
	2. [Activating Browser Modes with Doctype](https://hsivonen.fi/doctype/)