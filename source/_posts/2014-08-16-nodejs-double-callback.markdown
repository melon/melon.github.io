title: "nodejs两次回调问题"
date: 2014-08-16 00:18
categories:
- IT
tags:
- nodejs
- javascript
---
研究了一下nodejs的double callback问题，发现原来是这样的：
在try/catch中的try语句和catch语句中都有调用callback操作时，如果callback函数本身会抛错，就会引发double callback.

我们来看具体例子

{% code lang:js %}
var count = 1;
function test(cb) {
    try {
        cb(null, true);
    } catch (e) {
        console.log('error caught');
        cb(e);
    }
}
test(function (err, throwError) {
    console.log('callback invoked', count++);
    if (throwError) {
        throw new Error('xxx');
    }
});
// 结果
// callback invoked 1
// error caught
// callback invoked 2
{% endcode %}
从上面的例子可以看出，当cb函数抛错的时候，try语句就会捕捉到该错误，从而导致触发catch语句，而catch语句中存在cb的调用，因而会导致cb函数被第二次调用。
