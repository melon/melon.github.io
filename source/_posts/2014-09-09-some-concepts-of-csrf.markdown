title: "跨站请求伪造(CSRF)的一些概念"
date: 2014-09-09 15:51
categories:
- IT
tags:
- frontend
- security
---
跨站请求伪造，Cross-site request forgery，简写成CSRF或XSRF。[这里](http://www.veracode.com/security/csrf)有一段关于CSRF的定义

<blockquote>
Cross-Site Request Forgery (CSRF) is an attack outlined in the OWASP Top 10 whereby a malicious website will send a request to a web application that a user is already authenticated against from a different website. This way an attacker can access functionality in a target web application via the victim's already authenticated browser.
</blockquote>

看了之后还是一头雾水？恩！那是正常的！莫担心你自己的理解力。。。

我们还是来个实际的具体一点的例子。假设有一个银行，当你登录后，就能通过以下链接来方便的转账了

{% code lang:html %}
https://www.sbbank.com/transferFunds?to=小朱&amount=15000
{% endcode %}

请求这个链接之后，就能将你账户中的15000元转给小朱了。乍看之下没什么问题，因为你已经登录了，没登录的话是没有权限进行转账的。但是加入有某个恶意网站www.diaoyu.com在它网站的代码里偷偷加了这么一句代码

{% code lang:js %}
<iframe src="https://www.sbbank.com/transferFunds?to=盗贼&amount=10000"></iframe>
{% endcode %}

然后有人故意将这个恶意网站的网址，比如说http://www.diaoyu.com/timeline.html，评论到某篇博客或者某条微博下，并且用了一些花言巧语来引诱你点击。要是你稍微一不留意，真点了那链接，那你的钱就自动地转到盗贼的账户里了！在实际操作中有很多方法实现伪造请求，比如说用图片标签img的src属性

{% code lang:js %}
<img src="https://www.sbbank.com/transferFunds?to=盗贼&amount=10000">
{% endcode %}

其实压根就不是一张图，但是浏览器会去发这个请求。

当然，现实中的银行肯定不会这么不安全，上面的这个例子只是一个比较理想的设想罢了，是为了帮助大家更快地理解CSRF这个东西。现实中绝对不应该用GET方法就能实现数据状态的改变，所以一旦使用POST请求，上面的两种CSRF就完全失效了。例如用Ajax发送一个跨域POST请求

{% code lang:js %}
$.ajax({
    url: 'https://www.sbbank.com/transferFunds',
    data: {
        to: '盗贼',
        amount: 10000
    }
});
{% endcode %}

但是，对于现代浏览器来说，默认情况下受限于同源安全策略（Same-Origin Policy），上述Ajax请求将会被阻止。只有对于较老的浏览器或者主动开启了跨域资源共享（CORS）的情况下，上述攻击才有可能得逞。所以为了安全，我们应该尽量使用现代浏览器，并且对开发者来说，如非必要，尽量禁用CORS。

总结一下，本质上CSRF是利用特殊的手段去欺骗浏览器，让浏览器帮着你去实施劫持。很多人认为CSRF产生的根本原因是协议的缺陷，即http/https请求每次访问的时候都会<strong>自动</strong>带上cookie。对于CSRF攻击，除了开发者需要采取各种措施来保证安全，用户也应该尽量抛弃老浏览器，同时及时更新浏览器。现实中用纯CSRF实施攻击的例子貌似不是太多，一般是辅助实施攻击。有意思的是，实际的例子也确实曾经存在过，攻击者利用uTorrent的页面客户端插件来实施攻击，感兴趣的可以去[这里](http://xs-sniper.com/blog/2008/04/21/csrf-pwns-your-box/)了解详细情况。


- References:
    1. [Cross-Site Request Forgery Guide: Learn All About CSRF Attacks and CSRF Protection](http://www.veracode.com/security/csrf)
    2. [Cross-site request forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery)
    3. [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29)