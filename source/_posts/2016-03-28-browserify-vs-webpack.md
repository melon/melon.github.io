---
title: browserify和webpack
date: 2016-03-28 11:59:30
categories:
- IT
tags:
- webpack
- browserify
---
browserify最核心的思想是让node代码也在能浏览器端执行，核心思想之外的东西则全都交给了`transform`和`plug-in`。而webpack则把一部分前端自动化中的步骤，如代码压缩、代码打包，整合到了wepback的核心思想中。打个比方，用browserify就像在一片空地上造房子，而用webpack就相当于在已经有了房屋的架子的基础上进行修修改改。

browserify天然只支持CommonJS的模块加载格式，而webpack对CommonJS、AMD加载格式都天然支持。当然，browserify可以通过使用插件（如deAMDify）来支持AMD的写法。

browserify原生不支持代码拆分（Code Splitting），而webpack支持，这也是它的一大特点。但是browserify通过使用插件（如Factor-Bundle），也可以支持代码拆分。

webpack的社区流行度似乎更高，vuejs、react似乎都倾向于使用webpack作为它们的模块加载工具。

browserify只适用于js资源，而webpack除了js资源，还能作用于css、图片等资源。

browserify通常需要和grunt、gulp一起配合使用（比如必须要使用它们来处理css），但webpack基本不需要。

webpack支持热替换（Hot Module Replacement），无须刷新页面就能实现页面更新（这跟自动刷新还是有区别的）。如果使用ReactJS话，react-hot-loader很赞，但browserify通过插件（livereactload）也能支持。

browserify和webpack和grunt一样都是配置式的，跟gulp不同，但很多观点认为配置式并无不好之处，很多时候只是不同人有不同的喜好而已。

webpack在开发的时候能实现自动编译只发生过更改的模块，速度回非常快，browserify通过插件也能实现类似功能。

References:
- [https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9)
- [http://blog.namangoel.com/browserify-vs-webpack-js-drama](http://blog.namangoel.com/browserify-vs-webpack-js-drama)
- [https://www.quora.com/What-are-the-pros-and-cons-of-Browserify-versus-Webpack](https://www.quora.com/What-are-the-pros-and-cons-of-Browserify-versus-Webpack)
