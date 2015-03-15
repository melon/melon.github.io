title: repaint和reflow的概念
date: 2015-03-15 14:56:26
categories:
- IT
tags:
- css
- performance
- repaint
- reflow
---
## I. repaint

repaint(或叫redraw)发生在当一个元素从可见变成不可见或者从不可见变成成可见，但是**并未发生布局上的变动**的时候。

发生在以下情况的时候

- 给某元素加了个outline
- 改变了某元素的背景颜色
- 改变了某元素的visibility属性
- 其他

repaint是比较耗时的，因为所有相关的元素都需要检查一遍从而确定是否展现和怎么展现。

## II. reflow

<!--more-->

reflow是一种更为“重大”的操作。以下情况都会触发reflow操作

- DOM树发生变化的时候
- 当改动了一个样式导致布局发生变化的时候
- 当一个元素的className发生变化的时候
- 当浏览器resize的时候
- 其他

当某元素被触发reflow动作后，与它相关的元素都同样要触发reflow，除此之外，布局上在它之后的元素也会触发，它的父级元素也会触发。而且reflow之后，所有这些元素都要repaint一遍。

reflow是导致DOM脚本性能慢的一个主要的原因，尤其是对于移动设备这样的低处理能力的终端。很多时候，reflow就相当于整个页面重新渲染一遍。

## III. 一些措施

### 绝对定位

定位方式为absolute或fixed的的元素，它们不会影响主文档的布局，所以当它们reflow了，并不会触发主文档的reflow。虽然主文档可能会repaint一下，但相比reflow，这消耗少多了。

### 不直接在显示的元素上多次更改

由于每做一次DOM操作，都会触发reflow一次，所以，尽量合并DOM操作。

例如现在一个未显示的Document Fragment里做操作，最后一次性附到显示的DOM中去

{% code lang:js %}
var docFragm = document.createDocumentFragment();
var elem, contents;
for(var i = 0; i < textlist.length; i++) {
	elem = document.createElement('p');
	contents = document.createTextNode(textlist[i]);
	elem.appendChild(contents);
	docFragm.appendChild(elem);
}
document.body.appendChild(docFragm);
{% endcode %}

另外也可以先深度拷贝一份dom，更改完之后再替换。

{% code lang:js %}
var original = document.getElementById('container');
var cloned = original.cloneNode(true);
cloned.setAttribute('width', '50%');
var elem, contents;
for(var i = 0; i < textlist.length; i++) {
	elem = document.createElement('p');
	contents = document.createTextNode(textlist[i]);
	elem.appendChild(contents);
	cloned.appendChild(elem);
}
original.parentNode.replaceChild(cloned, original);
{% endcode %}

但要注意如果元素内含有form类子元素，这种方法会丢失用户已填的信息（？）。另外当子元素有事件绑定时，也不建议用这种方法。

### 先隐藏再修改

当一个元素dispay设置成none之后，即使内容发生变化，也不会repaint。利用这个特点，当内容改动比较多时，先隐藏元素，最后再显示出来是能提升效率的。

{% code lang:js %}
var posElem = document.getElementById('animation');
posElem.style.display = 'none';
posElem.appendChild(newNodes);
posElem.style.width = '10em';
// Other changes…
posElem.style.display = 'block';
{% endcode %}

但注意这种方法可能会造成界面上的跳跃，与滚动条有关。

### 获取尺寸之后缓存起来备用

注意有些获取尺寸的方法如offsetWidth(offsetHeight)，以及getComputedStyle方法都会强制触发reflow。所以必须要获取时，获取之后可以存起来重用。

{% code lang:js %}
var posElem = document.getElementById('animation');
var calcWidth = posElem.offsetWidth;
posElem.style.fontSize = (calcWidth / 10) + 'px';
posElem.firstChild.style.marginLeft = (calcWidth / 20) + 'px';
posElem.style.left = ((-1 * calcWidth) / 2) + 'px';
// Other changes…
{% endcode %}

### 多次样式改动可以合并

像下面这样

{% code lang:js %}
var toChange = document.getElementById('mainelement');
toChange.style.background = '#333';
toChange.style.color = '#fff';
toChange.style.border = '1px solid #00f';
{% endcode %}

三条样式改动语句就触法了三次reflow

改进方法一是改用class，只需一次加className的操作和一次reflow

{% code lang:js %}
document.getElementById('mainelement').className = 'highlight';
{% endcode %}

或者还是用style

{% code lang:js %}
var posElem = document.getElementById('animation');
var newStyle = 'background: ' + newBack + ';' +
	'color: ' + newColor + ';' +
	'border: ' + newBorder + ';';
if(typeof(posElem.style.cssText) != 'undefined') {
	posElem.style.cssText = newStyle;
} else {
	posElem.setAttribute('style', newStyle);
}
{% endcode %}


- References:
  1. [Efficient JavaScript](https://dev.opera.com/articles/efficient-javascript/)
