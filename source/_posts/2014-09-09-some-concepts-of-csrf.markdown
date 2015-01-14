title: "跨站请求伪造(CSRF)的一些概念"
date: 2014-09-09 15:51
categories:
- IT
tags:
- frontend
- javascript
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

<del>当然，现实中的银行肯定不会这么不安全，上面的这个例子只是一个比较理想的设想罢了，是为了帮助大家更快地理解CSRF这个东西。现实中绝对不应该用GET方法就能实现数据状态的改变，所以一旦使用POST请求，上面的两种CSRF就完全失效了。例如用Ajax发送一个跨域POST请求</del>

<del>但是，对于现代浏览器来说，默认情况下受限于同源安全策略（Same-Origin Policy），上述Ajax请求将会被阻止。只有对于较老的浏览器或者主动开启了跨域资源共享（CORS）的情况下，上述攻击才有可能得逞。所以为了安全，我们应该尽量使用现代浏览器，并且对开发者来说，如非必要，尽量禁用CORS。</del>

**EDIT:**
我之前关于同源策略能用来防止CSRF的看法是错误的，**同源策略是指在没有明确授权的情况下，不同源的站点之间无法互相分享各自的资源。**比如一个黑客做到站点嵌入一个iframe，iframe中是嵌入的假设是你的gmail页面，倘若没有同源策略限制，有一天你意外访问到这个黑客的站点，而且恰巧你也登陆了gmail，那么这个站点的页面，作为父页面，就能读取你的gmail邮箱里的内容了！这会有多么可怕！然而同源策略却不是万能的，它能组织那个站点去读取你的邮件，但却无法组织这个站点向gmail网站发请求，就是“嘿！------老子爱给谁发请求是老子的自由，你管得着么？”这个意思。

所谓CSRF，通俗一点讲就是，某个网站知道某个请求是合法的（有合法的session的，确实是从登录后的用户那端发过来的），但是该网站却无法分辨出这个请求是你的**主动**行为，还是某些网站、某些脚本背着你偷偷给发出去的。

目前防止CSRF的有效方法，我搜了一下资料，发现貌似只有采用CSRF令牌（CSRF token），也即只有请求在附上一个特殊的token之后，这个请求才是有效的，而这个token可能是和用户当前session等有关的一个值，一个不容易被黑客猜出来的值。
**END EDIT**

<del>总结一下，本质上CSRF是利用特殊的手段去欺骗浏览器，让浏览器帮着你去实施劫持。很多人认为CSRF产生的根本原因是协议的缺陷，即http/https请求每次访问的时候都会<strong>自动</strong>带上cookie。对于CSRF攻击，除了开发者需要采取各种措施来保证安全，用户也应该尽量抛弃老浏览器，同时及时更新浏览器。现实中用纯CSRF实施攻击的例子貌似不是太多，一般是辅助实施攻击。有意思的是，实际的例子也确实曾经存在过，攻击者利用uTorrent的页面客户端插件来实施攻击，感兴趣的可以去[这里](http://xs-sniper.com/blog/2008/04/21/csrf-pwns-your-box/)了解详细情况。</del>


- References:
    1. [Cross-Site Request Forgery Guide: Learn All About CSRF Attacks and CSRF Protection](http://www.veracode.com/security/csrf)
    2. [Cross-site request forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery)
    3. [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29)