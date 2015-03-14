title: 图片懒加载设计要点
date: 2015-03-14 21:17:16
categories:
- IT
- Notes
tags:
- image loading
- performance
- javascript
---
看了一下图片懒加载的相关文章，总结了一些设计要点

1. html下的img标签中首先会给定一个通用的背景图的src，然后一般会指定一个data-src，用来表示真实的图片地址

{% code lang:js %}
<img src="bg.png" data-src="tree.jpg">
{% endcode %}

2. 图片一旦进入viewport，就会发生用data-src的值替换src值的过程

3. 侦听scroll,resize等事件，触发后去判断出所有在viewport（或提前一定的量，比如在viewport之外200px也算）里的图片便签

4. 具体判断某张图是否在viewport范围内的方法：满足以下之一即为在范围内
    - 该图的下边缘是否在viewport的上边缘之下
    - 该图的上边缘是否在viewport的下边缘之上

- References:
  1. [unveil/jquery.unveil.js](https://github.com/luis-almeida/unveil/blob/master/jquery.unveil.js)
  2. [Lazy Loading Images](https://css-tricks.com/snippets/javascript/lazy-loading-images/)
