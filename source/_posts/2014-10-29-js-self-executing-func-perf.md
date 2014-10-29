title: javascript中各种写法的自执行函数性能对比
date: 2014-10-29 17:37:21
categories:
- IT
tags:
- frontend
- javascript
- performance
---
面对各种写法的自执行函数时，我们应该选择哪个?

jsperf上有人写了个[性能对比测试](http://jsperf.com/self-invoking-function)

![性能对比图](http://ww2.sinaimg.cn/large/9047644ajw1els4yhcnf3j20qx0hcgnu.jpg)

可以看到void和delete性能相对较高，而前置运算符和加括号的方式性能相对会比较低。如果单从性能上来说，我比较偏向于使用void（delete有点文不对题的感觉）。

其次，从希望函数表达式返回的结果类型（是Number类型还是Bool类型），也是一个选择的判断标准。

最后要说的是，正如这个测试作者总结的，虽说性能是一大考虑因素，但是使用括号和！还是比较推荐的写法，因为它们更加符合javascript的语言风格。