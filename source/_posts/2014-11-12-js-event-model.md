title: javascript事件模型中的顺序
date: 2014-11-12 14:40:28
categories:
- IT
tags:
- javascript
---
最近在用react重构代码的时候，看到了react里的一个封装好的[事件小模块](https://github.com/facebook/react/blob/master/src/vendor/stubs/EventListener.js)：

{% code lang:js %}
var emptyFunction = require('emptyFunction');

/**
 * Upstream version of event listener. Does not take into account specific
 * nature of platform.
 */
var EventListener = {
  /**
   * Listen to DOM events during the bubble phase.
   *
   * @param {DOMEventTarget} target DOM element to register listener on.
   * @param {string} eventType Event type, e.g. 'click' or 'mouseover'.
   * @param {function} callback Callback function.
   * @return {object} Object with a `remove` method.
   */
  listen: function(target, eventType, callback) {
    if (target.addEventListener) {
      target.addEventListener(eventType, callback, false);
      return {
        remove: function() {
          target.removeEventListener(eventType, callback, false);
        }
      };
    } else if (target.attachEvent) {
      target.attachEvent('on' + eventType, callback);
      return {
        remove: function() {
          target.detachEvent('on' + eventType, callback);
        }
      };
    }
  },

  /**
   * Listen to DOM events during the capture phase.
   *
   * @param {DOMEventTarget} target DOM element to register listener on.
   * @param {string} eventType Event type, e.g. 'click' or 'mouseover'.
   * @param {function} callback Callback function.
   * @return {object} Object with a `remove` method.
   */
  capture: function(target, eventType, callback) {
    if (!target.addEventListener) {
      if (__DEV__) {
        console.error(
          'Attempted to listen to events during the capture phase on a ' +
          'browser that does not support the capture phase. Your application ' +
          'will not receive some events.'
        );
      }
      return {
        remove: emptyFunction
      };
    } else {
      target.addEventListener(eventType, callback, true);
      return {
        remove: function() {
          target.removeEventListener(eventType, callback, true);
        }
      };
    }
  },

  registerDefault: function() {}
};

module.exports = EventListener;
{% endcode %}

<!--more-->

从上面的代码可以看到针对两种事件顺序：**事件冒泡（event bubbling）**和**事件捕获（event capturing）**，分别采用了listen和capture函数。事件冒泡又有两种实现方法**addEventListener**和**attachEvent**两种，而事件捕获却只有一种。对于这些诡异的情况，难免会让人心声疑惑：为什么一个看似很简单的事件模型，需要这么多不同的实现方式？

查阅了一些资料之后，找到了一些解释，总结来说，就是“历史遗留问题”。

## 两种模型

首先下面有个示意图，表示两个元素

```
-----------------------------------
| element1                        |
|   -------------------------     |
|   |element2               |     |
|   -------------------------     |
|                                 |
-----------------------------------
```

如果点击了element2，那么两个元素的onClick事件回调都会被触发。然后问题来了，到底谁先触发？

对于这个问题，当年的两大浏览器巨头Netscape和Microsoft产生了分歧：

- Netscape认为element1的事件首先应该被触发。这种方式被称为事件捕获。
- Microsoft认为element2的事件应该首先被触发。这种方式被称为事件冒泡。

### 事件捕获

```
               | |
---------------| |-----------------
| element1     | |                |
|   -----------| |-----------     |
|   |element2  \ /          |     |
|   -------------------------     |
|        Event CAPTURING          |
-----------------------------------
```

### 事件冒泡

```
               / \
---------------| |-----------------
| element1     | |                |
|   -----------| |-----------     |
|   |element2  | |          |     |
|   -------------------------     |
|        Event BUBBLING           |
-----------------------------------
```

## W3C模型

W3C组织综合考虑了以上两种，采用了第三种模型：事件会首先按从外层到内层的顺序依次被“捕获”，紧接着又按从内层到外层的顺序依次“冒泡”。即事件处理含有两个阶段。

```
                 | |  / \
-----------------| |--| |-----------------
| element1       | |  | |                |
|   -------------| |--| |-----------     |
|   |element2    \ /  | |          |     |
|   --------------------------------     |
|        W3C event model                 |
------------------------------------------
```

而以上过程是通过**addEventListener**这个函数来实现的。addEventListener的第三个参数是是否捕获的标识位，如果为true，就表示将事件注册在捕获阶段，否则，就将事件注册在冒泡阶段。

举个例子，如果事件按下列方式注册

{% code lang:js %}
element1.addEventListener('click',callback1,true)
element2.addEventListener('click',callback2,true)
element1.addEventListener('click',callback3,false)
element2.addEventListener('click',callback4,false)
{% endcode %}

那么当点击element2的之后，事件回调执行的顺序就是
callback1 ----> callback2 ----> callback4 ----> callback3


## 总结

看到这里，你大概就能理解为什么上面那个事件小模块的事件捕获capture函数里为什么没有attachEvent函数的相应实现了：因为最初的最初，微软就压根不认同捕获模型，所以就不会有支持捕获模型的attachEvent函数的诞生。而到后来，又由于标准化的需要，事件的注册都推荐用addEventListener了，自然attachEvent也就不需要演化了，而是应该逐渐被淘汰。可惜ie8中还依赖attachEvent。。。

但其实这种兼容性处理的方法还是会有点小问题的，其中我认为影响最大的就是currentTarget的问题了。用attchEvent这种过方式，或者说在ie8下，触发的事件回调函数的第一个参数event里并不包含currentTarget这个属性，而且似乎没有很好的获得currentTarget的替代方法。对于我这种经常用到e.currentTarget的人，这简直是噩梦。。。所以要是能不兼容ie8那就果断抛弃吧，那样连attachEvent也压根都不需要考虑了！

- References:
	1. [react/src/vendor/stubs/EventListener.js](https://github.com/facebook/react/blob/master/src/vendor/stubs/EventListener.js)
	2. [Event order](http://www.quirksmode.org/js/events_order.html)