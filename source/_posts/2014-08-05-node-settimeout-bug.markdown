title: "node v0.10.30下的setTimeout问题"
date: 2014-08-05 14:44
categories:
- IT
tags:
- nodejs
---
最近把node从v0.10.29更新到v0.10.30之后，之前一直运行好好的爬虫脚本出现了问题，并且一段时间之后node进程挂起，cpu占用达到100%，用不了多久服务器的8核全部被占慢。这是在我收到服务器报警邮件之后才得知的，然后就开始去查找原因了。

在一段时间的debug之后，我发现问题似乎是出在setTimeout这个函数上，每次都是发现用setTimeout延迟一段时间之后，目标函数并未如期执行。

于是就去google了一下，看了一下node官网的[changelog](http://nodejs.org/changelog.html)，对比发现在v0.10.29的基础之上确实有关于timer的修改，我猜测可能是这个bug改动引发的新bug。

果不其然，在joyent的ndoe项目下，查找了最近的issue，发现了[setTimeout causing full cpu usage with non-integer durations #8065](https://github.com/joyent/node/issues/8065)。issue大致的意思是说当setTimeout的delay时间如果是float类型的话，node程序运行一会儿之后就会莫名其妙地被挂起，并且cpu占用达到100%。我去检查了一下自己的代码，发现我也是用了Math.random来随机生成一个延迟时间，因此确实会产生一个float类型的参数。

为了进一步验证，我用TJ写的[n](https://github.com/visionmedia/n)将node版本切换会v0.10.29，之前的问题消失了。

值得庆幸的是，v0.10.30中timer bug更改的作者[Julien Gilli](https://github.com/misterdjules)很快回应了这个issue，并且解决了这个新的bug:[timers: fix timers with non-integer delay hanging. #8073](https://github.com/joyent/node/pull/8073)，估计在下一个稳定版本中，这个bug就会被修复了。在这之前，如果程序收到这个bug的影响，那估计唯一的办法就是退回旧版本了。

经过这次，我突然发现其实node的版本切换管理程序确实挺有实用价值的，推荐大家安装，这样就可以在不同稳定版、甚至在稳定版本与开发版本之间随意切换了。

<strong>Eidt(2014-08-20):</strong>
在新发布的[v0.10.31](https://raw.githubusercontent.com/joyent/node/v0.10.31/ChangeLog)版本中已经修复了这个bug
<pre>
2014.08.19, Version 0.10.31 (Stable)
...
* timers: fix timers with non-integer delay hanging. (Julien Gilli)
</pre>