---
title: 一个webpack的煎蛋demo
date: 2016-03-16 14:03:08
categories:
- IT
tags:
- webpack
---
从各方面的消息能明显感觉到大家对webpack的评价很高，webpack是个配置很灵活的功能强大的前端工程自动化的工具，但是webpack有个让很多人都感受的到的缺陷就是它的文档实在是不能说写得很好。很多初学者都不知道怎么开始使用webpack，webpack的文档的严重影响了它的普及。

下面我就简单讲一个使用webpack的例子，让大家对webpack有个初步的感知。

### 创建新工程

毫无疑问，我们使用git来进行代码的版本管理。

```bash
mkdir webpack-example
cd webpack-example
echo 'a webpack example' > README.md
git init
touch .gitignore
```

首先我们新建一个文件夹作为工程目录，创建`README.md`文件，并初始化git配置，同时可以按情况创建`.gitignore`文件

### 初始化npm

近期比较流行的一种前端技术栈是

- 使用npm来管理依赖
- 使用babel来让我们使用ES6/ES7语法
- 使用webpack来处理文件之间的相互依赖关系和代码的编译

所以接下来我们首先需要初始化npm，将所有依赖的模块都引入。

```bash
npm init
```

执行上述命令，按照实际情况输入，完成之后就会有个初始化的`package.json`文件。

### 引入babel依赖

使用最新的javascript规范的语法来书写我们的代码能提升我们的开发体验，但是现在浏览器对新规范的支持情况又让开发者对这些令人着迷的新特性望而却步，鉴于这样一种矛盾的情况，babel就应运而生了。

babel可以和很多其他的技术栈结合应用，从这个[setup](http://babeljs.io/docs/setup/)页就能可见一斑。本文讨论的情况是babel和webpack的结合运用。

我们需要引入babel的三个模块：`babel-core`，`babel-loader`和`babel-preset-es2015`。

```bash
npm install --save-dev babel-core babel-loader babel-preset-es2015
```

`babel-core`是babel的核心代码，`babel-loader`是当babel和webpack结合使用时需要载入的一个包，然而只有这两个，babel并不会为你编译转换代码，你还需要引入一个`preset`。

所谓`preset`，可以看成语法规范，引入之后就能支持对符合引入规范的js语法进行编译转换。babel支持的所有preset可以去[这里](http://babeljs.io/docs/plugins/)了解。

### 引入webpack依赖

babel已经引入，接下来引入webpack

```bash
npm install --save-dev webpack
```

安装好webpack之后，最后的重头戏就是webpack的配置了，从这里开始，webpack的灵活性就体现出来了，配置方式五花八门，可以根据项目的实际情况任你挑选。本文会对一个简单的多入口文件的例子进行配置。

配置可以直接作为webpack的参数传入，但是更好的方式是使用配置文件。

### 配置webpack

假设项目的文件结构如下

```
webpack-example
    ├── build
    ├── js
    │   ├── pageA.js
    │   ├── pageB.js
    │   ├── shared.js
    │   └── shared2.js
    ├── html
    │   ├── pageA.html
    │   └── pageB.html
    ├── .gitignore
    ├── package.json
    ├── README.md
    └── webpack.config.js
```

`js`目录下的`pageA.js`和`pageB.js`是两个入口文件，会被两个页面`pageA.html`和`pageB.html`分别引用。`pageA.js`依赖`shared.js`和`shared2.js`两个js文件，`pageB.js`依赖`shared.js`文件。

在这个背景下，webpack要做的就是将所有js文件进行打包，最终形成两个html页面需要引入的打包后的js文件。为此，`webpack.config.js`可以这么写

```js webpack.config.js
var path = require('path');
var AssetsPlugin = require('assets-webpack-plugin');
var assetsPluginInstance = new AssetsPlugin({
    path: path.join(__dirname, 'build')
});

module.exports = {
    // 指定入口文件
    entry: {
        pageA: './js/pageA.js',
        pageB: './js/pageB.js',
    },
    // 输出配置
    output: {
        path: path.join(__dirname, 'build/js/'),
        filename: '[name].bundle.[chunkhash].js',
        chunkFilename: '[id].chunk.[chunkhash].js',
    },
    // 模拟代码中出现的node相关的模块（不写的话，在引入node自身的模块时会引起报错）
    node: {
        path: 'empty',
        fs: 'empty'
    },
    module: {
        loaders: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                // 指定loader为babel
                loader: 'babel',
                query: {
                    // 指定preset
                    presets: ['es2015']
                },
            },
        ]
    },
    // 引入额外插件
    plugins: [assetsPluginInstance]
};
```

可以发现我们引入了一个叫`assets-webpack-plugin`的插件，所以我们得执行

```bash
npm install --save-dev assets-webpack-plugin
```

这个插件的作用是把编译前的文件名和编译后的文件名对应起来形成一个映射对象，并写入文件中。

各个js文件的内容如下

```js pageA.js
require(["./shared", "./shared2"], function(shared, shared2) {
    shared("This is page A");
    shared2("This is page A");
});
```

```js pageB.js
require(["./shared"], function(shared) {
    shared("This is page B");
});
```

```js shared.js
module.exports = function(msg) {
    console.log(1, msg);
};
```

```js shared2.js
module.exports = function(msg) {
    console.log(2, msg);
};
```

接着我们执行

```bash
./node_modules/.bin/webpack webpack.config.js
```

就能在build目录下看到编译后的相关文件

```
build
  ├── js
  │   ├── 2.chunk.4217cd33df3cca54e202.js
  │   ├── 4.chunk.d2804f94e850044225d1.js
  │   ├── main.bundle.25c68114fd6604d4f2d5.js
  │   ├── pageA.bundle.14aba2165c3c2e4ffb06.js
  │   └── pageB.bunlde.b07d4b8d5c9da565424a.js
  └── webpack.assets.json
```

### 结束语

上面就是一个简单的demo尝试，然而，我并没有将如何将这些带hash值的文件名嵌入到html文件中去。简单说一下两个思路，一个是动态模板的思路，因为assets-webpack-plugin会生成一个资源映射文件，所以只要在动态模板生成的时候用函数调取这个映射文件匹配资源从而动态生成资源名然后嵌入至html中；另一个思路是想办法解析html然后自动替换相应资源名，这个思路如果用gulp来实现已经就有现成的插件了，但是我希望的是尽量不用gulp而用纯粹的webpack来实现，为了实现这个目标，目前看来只能自己写loader或plugin了。
