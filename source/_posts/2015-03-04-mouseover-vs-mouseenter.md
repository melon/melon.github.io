title: mouseover和mouseenter的区别
date: 2015-03-04 23:10:58
categories:
- IT
tags:
- javascript
- event
- mouseover
- mouseenter
---
mouseover:
当鼠标移动到带有该事件绑定的元素**或由这样的元素移动到其子元素**时，该事件被触发

mouseenter:
当鼠标移动到带有该事件绑定的元素时，该事件被触发

事实上，mouseenter在IE中是支持的，但在Firefox,Chrome以及Safari中并不支持。jQuery中mouseenter在所有浏览器里都支持是因为它在这些不支持的浏览器中模拟了这个事件。但是mouseenter事件已经被写入最新的DOM Level 3规范中，这些浏览器的原生支持理论上会逐步实现，因为要遵守规范。

附上两个demo
[mouseover](http://jsfiddle.net/XXXLtender/yuzqv5pL/)
[mouseenter](http://jsfiddle.net/XXXLtender/a7bLyLmz/)


- References:
  1. [Event reference](https://developer.mozilla.org/en-US/docs/Web/Events)
  2. [Event compatibility tables](http://www.quirksmode.org/dom/events/)
  3. [.mouseenter()](http://api.jquery.com/mouseenter/)
  4. [.mouseover()](http://api.jquery.com/mouseover/)
  5. [mouseenter](https://developer.mozilla.org/en-US/docs/Web/Events/mouseenter)
