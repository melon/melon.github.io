title: 【视频】instagram前端架构
date: 2015-01-27 17:16:13
categories:
- IT
- Notes
tags:
- architecture
- react
- instagram
- pete hunt
- css
---
pete hunt的演讲，很让我受启发的一个视频，视频中充满干货，需要花时间细细深究。

一些观点：

不是为了单页面而单页面，而是单页面确实能提升用户体验。

以文件来划分各种代码的的做法似乎有些僵化了，应该用模块化的方式。不单单是js代码模块化，css、图片都可以模块化。

css是个很让人头疼的东西，没有context概念，一条css规则就能“污染”全局，而且，一旦污染，还去不掉。所以目前来说，为了适应模块化的思路，css编写应该严格遵循一些规则，理想的做法是css的atomic化。

browserify, requirejs, grunt, gulp都不行，选择天才之作webpack吧。

<!--more-->

附上视频：

<iframe width="560" height="315" src="//www.youtube.com/embed/VkTCL6Nqm6Y" frameborder="0" allowfullscreen></iframe>