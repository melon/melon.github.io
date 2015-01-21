title: css中字体的rem尺寸
date: 2015-01-21 11:54:14
categories:
- IT
tags:
- css
- rem
---

目前业界有两种常用的css字体尺寸设置的单位

1. 用px设置
2. 用em设置


### 用px设置

总体来说是可靠的方案。

但是老版本IE，甚至是IE9，浏览器内置的放大缩小功能都无法影响到字体的大小。最新的版本已经和其他主流浏览器一样可以支持字体的放大缩小了。

### 用em设置

用来解决上述px设置带来的问题

{% code lang:css %}
html { font-size: 62.5%; } /* html默认字体大小为16px，62.5%为10px */
ul { font-size: 1.4em; } /* 相对于上一级扩大1.4倍，为14px */
li { font-size: 1.4em; } /* 相对于上一级扩大1.4倍，为19.6px！ */
{% endcode %}

但是有个新问题，用em设置的字体大小的参考基准是上一级，对当前这一级来说，上一级永远是1em，因此会出现字体大小叠加的情况，这是我们不太愿意看到的。

{% code lang:css %}
li { font-size: 1em; }
{% endcode %}

现实中往往是采用上面的思想来解决这个新问题。

总结，还是不理想。

### 用rem设置

CSS3引入了新的单位，rem，表示“root em”。于em不同，rem的参考基准永远是html的字体大小设置。

{% code lang:css %}
html { font-size: 62.5%; }
body { font-size: 14px; font-size: 1.4rem; } /* =14px */
h1   { font-size: 24px; font-size: 2.4rem; } /* =24px */
{% endcode %}

浏览器支持度：Safari 5+， Chrome, Firefox 3.6+, IE9+

总结，几乎完美。

- References:
	1. [FONT SIZING WITH REM](http://snook.ca/archives/html_and_css/font-size-with-rem)