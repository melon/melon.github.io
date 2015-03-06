title: 一种神奇的绝对定位居中方式
date: 2015-03-06 10:26:52
categories:
- IT
tags:
- css
- centering
- w3c
---
这是一种不常见的水平垂直居中方法，只要被居中的目标元素的高度是指定的，同时附加上以下设置，就可以实现居中。

{% code lang:css %}
.Absolute-Center {
  margin: auto;
  position: absolute;
  top: 0; left: 0; bottom: 0; right: 0;
}
{% endcode %}

demo可以看这个[jsFiddle](http://jsfiddle.net/mBBJM/1/)

这中居中方式有一些很明显的优点
* 支持IE8
* 可以居中任何东西
* 对于百分比和min-/max-能做到响应式支持

为什么这个方法会有这种特效？

D U A N G ~

其实这是规范中的内容所导致的，CSS2.1的第10章Visual formatting model details里主要讲了各种情况下元素的尺寸应该如何计算的问题。

其中宽度上对于absolutely positioned, non-replaced elements有一句话

If none of the three(left,width,right) is 'auto': If both 'margin-left' and 'margin-right' are 'auto', solve the equation under the extra constraint that the two margins get **equal values**

高度上

If none of the three(top,height,bottom) are 'auto': If both 'margin-top' and 'margin-bottom' are 'auto', solve the equation under the extra constraint that the two margins get **equal values**

意思是，当width,left,right属性都不是'auto'的时候，并且margin-left和margin-right都是'auto'，这种情况下，浏览器会保证margin-left和margin-right相等；宽度也同理。

有了这两条理论依据的支持，上面的居中方法的神奇之处也不难理解了。

- References:
  1. [Absolute Horizontal And Vertical Centering In CSS](http://www.smashingmagazine.com/2013/08/09/absolute-horizontal-vertical-centering-css/)
  2. [How to Center Anything With CSS](http://designshack.net/articles/css/how-to-center-anything-with-css)
  3. [CSS2.1 10 Visual formatting model details](http://www.w3.org/TR/CSS21/visudet.html)
