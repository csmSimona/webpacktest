# webpacktest
学习webpack配置 https://www.jianshu.com/p/42e11515c10f 
## webpack

更多详情请阅读[webpack中文文档](https://www.webpackjs.com/concepts/)

#### 概念

##### 什么是webpack

本质上，*webpack* 是一个现代 JavaScript 应用程序的**静态模块打包器(module bundler)**。它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。

webpack可以将工程中的静态资源根据我们声明的依赖关系，打包出最后的正常运行的输出文件。官网显示的这幅图很形象地描述了这个过程：

![](C:\Users\chensm\Desktop\啦啦\博客\picture\webpack.png)

在webpack中，所有的静态资源都可以被处理为一个模块，包括js、图片、css、字体。

##### 模块化的好处

- 更轻松地拆分代码逻辑，对于大型工程尤其重要
- 更容易地去管理变量，模块会自动生成一个局部变量环境，减少全局变量的污染
- 显示地声明依赖关系，想想之前在head中引入很多script标签，由于script顺序错误而引起的bug

##### WebPack和Grunt以及Gulp相比有什么特性

其实Webpack和另外两个并没有太多的可比性，Gulp/Grunt是一种能够优化前端的开发流程的工具，而WebPack是一种模块化的解决方案，不过Webpack的优点使得Webpack在很多场景下可以替代Gulp/Grunt类的工具。

Grunt和Gulp的工作方式是：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，工具之后可以自动替你完成这些任务。

#### 入口(entry)

工程的入口文件配置，在这里的js文件以及在里面被引用的文件会被webpack找到打包。

进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

每个依赖项随即被处理，最后输出到称之为 *bundles* 的文件中。默认值为 `./src`。

配置写法有三种：

- 对象，可配置多入口，可配置chunk名，灵活可扩展，最常用,一个属性就是一个entry chunk

```JavaScript
module.exports = {
  entry: {
    app: './src/app.js',        //应用程序(app)入口
    vendors: './src/vendors.js' //第三方库(vendor)入口
  }
};
```

- 字符串, 最简单直接方式，单入口，chunk名默认为main

```javascript
module.exports = {
  entry: './path/to/my/entry/file.js'
};
//是下面的简写
const config = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
```

- 数组, 多入口，将多个入口文件打包为一个chunk，chunk名默认为main

```
module.exports = {
  entry: ['./path/to/my/entry/file.js', './path/to/my/entry/file1.js']
};
```

入口一般用对象写法即可，其他两种写法的可忽略。

##### 多页面应用程序

```javascript
//webpack.config.js
const config = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```

#### 输出(output)

打包的输出的文件配置，告诉 webpack 在哪里输出它所创建的 *bundles*，以及如何命名这些文件，默认值为 `./dist`。

Output用于配置打包输出的文件，包括输出文件的文件名、输出路径、静态资源地址，这里列出最常用的4种：

```javascript
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: 'js/[name].js',
    chunkFilename: 'js/[name].js',
    path: __dirname + '/dist',  //必须是绝对地址
    publicPath: 'http://cdn.example.com/assets/[hash]/'
  }
};
```

#### 加载器(loader)

Loader可以理解为不同类型模块的处理器，将这些类型的模块处理为浏览器可运行和识别的代码。比如babel-loader将es6以上代码转换为es5代码；sass-loader将sass代码解析为css代码；url-loader和file-loader可以将图片、字体等静态文件解析为base64码或者静态文件地址。Loaders给我们提供了处理模块的入口，在里面可以使用全部的js功能，从而使webpack具有了强大而灵活的能力。

> 注意，loader 能够 `import` *导入任何类型的模块（例如* `.css` 文件），这是 webpack 特有的功能，其他打包程序或任务执行器的可能并不支持。我们认为这种语言扩展是有很必要的，因为这可以使开发人员创建出更准确的依赖关系图。

1. `test` 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。

2. `use` 属性，表示进行转换时，应该使用哪个 loader。

   loader配置：

```javascript
modlue: {
    rules: [
          //js Loaders
        {
             test: /\.js$/,
             exclude: /(node_modules|bower_components)/,
             use: 'babel-loader'
        },
          //css Loaders
        {
            test: /\.sass$/,
            use: [ 'style-loader', 'css-loader', 'postcss-loader', 'sass-loader' ]
        }
    ]
}
```

#### 插件(plugins)

loader被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。比如代码分割、静态资源处理、环境变量的注入、将所有css的module抽取为单个文件等。webpack自身也是用插件系统构建起来的。插件的目的是做任何loaders做不了的事情。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。

想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建它的一个实例。

plugins配置：

```javascript
//webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

const config = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

#### 模式(mode)

通过选择 `none` 、`development` 或 `production` 之中的一个，来设置 `mode` 参数，你可以启用相应模式下的 webpack 内置的优化

```javascript
module.exports = {
  mode: 'production'
};
```
