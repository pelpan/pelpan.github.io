---
Elayout:    post
title:      Webpack 5 + React 布署
subtitle:   Webpack学习
date:       2022-07-25
author:     Pele
header-img: img/pic/post-bg-webpack.webp
catalog: true
tags:
    - 前端
    - 学习
---

# Webpack 5 + React

> intro: 尝试使用Webpack 打包React项目 并添加到HTML中

##### 开发环境
![](https://img.shields.io/static/v1?label=macOS&message=Monterey%2012.4&color=gray&logo=Apple&labelColor=black&style=flat)![](https://img.shields.io/static/v1?label=IDE&message=VS%20Code%201.69.2&color=gray&logo=VisualStudioCode&labelColor=%23007ACC)![](https://img.shields.io/static/v1?label=NodeJS&message=v16.13.0&color=gray&logo=node.js&logoColor=%23fff&labelColor=%23339933)![](https://img.shields.io/static/v1?label=React&message=v17.0.2&color=gray&logo=react&logoColor=%23000&labelColor=%2361DAFB)


## 前序知识

### Webpack

什么是Webpack?

> 本质上，**webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，然后将你项目中所需的每一个模块组合成一个或多个 *bundles*，它们均为静态资源，用于展示你的内容。

![webpack 搭建 react 项目](https://pic4.zhimg.com/v2-9626e988e504fd45bfb229967332f229_1440w.jpg?source=172ae18b)



## 布署

### 创建文件夹

创建一个空文件夹

使用npm命令初始化, 生成一个package.json文件

```bash
npm init -y
```

### 安装webpack

运行命令

```bash
npm install --save-dev webpack
```

安装完成后项目结构如下

```diff
|- node_modules
|- package-lock.json
|- package.json
```



### 创建config配置文件

在根目录下新建一个`config`文件夹, 并创建以下三个js文件

+ webpack.common.config.js
+ webpack.prod.config.js
+ webpack.dev.config.js

在根目录下创建一个`src`文件夹, 并新建一个`app.js` 文件

```diff
+ |- config
+   |- webpack.common.config.js
+   |- webpack.dev.config.js
+   |- webpack.prod.config.js
  |- node_modules
+ |- src
+   |- app.js
	|- package-lock.json
  |- package.json
```

在`webpack.common.config.js`中添加如下代码

```js
const path = require('path');

module.exports = {
  entry: {
    app: './src/app.js',
  },
  output: {
    filename: 'js/bundle.js',
    path: path.resolve(__dirname, '../dist'),
  },
};
```

这断代码的作用是指明了入口文件以及打包后的文件路径

在`package.json`配置文件中添加如下属性定义打包指令

```diff
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1",
+    "start": "webpack --config ./config/webpack.common.config.js"
   },
```

**运行一下试试**

```bash
npm run start
```

![image-20220725113351428](https://pele-images.oss-cn-hangzhou.aliyuncs.com/images/202207251133675.png)

可以看到生成了一个`dist`文件夹

```diff
  my-project
  |- config
+ |- dist
+   |- js
+     |- bundle.js
  |- node_modules
  |- src
    |- app.js
  |- package.json
```

此时`bundle.js`文件夹里还是空的, 因为`app.js`中还没有添加内容



### 安装 webpack-merge

```bash
npm install --save-dev webpack-merge
```

配置使用webpack-merge以生成更小的bundle, 更轻量的source map, 更优化的资源, 并改善加载时间, 以满足生产(production)的需求

修改`webpack.prod.config.js`

```js
const { merge } = require('webpack-merge');
const common = require('./webpack.common.config.js');

module.exports = merge(common, {
  mode: 'production',
});
```



在根目录下新建一个public文件夹, 并在其中新建一个`index.html`文件

```diff
  |- config
  |- node_modules
+ |- public
+   |- index.html
  |- src
    |- app.js
  |- package-lock.json
  |- package.json
```

向`index.html`中添加一点内容

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>my-project(webpack + react)</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
  <script src="../dist/js/bundle.js"></script>
</html>
```

向`app.js`中添加代码

```js
const root = getElementById("root");
root.innerHTML = "<h1>Hello World</h1>";
```

添加指令到`package.json`

```diff
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack --config ./config/webpack.common.config.js",
+   "build": "webpack --config ./config/webpack.prod.config.js"
  },
```

执行命令

```bash
npm run build
```

然后用浏览器打开`public/index.html`, 可以看到`app.js`中的内容已经成功添加到网页中

![image-20220725145433314](https://pele-images.oss-cn-hangzhou.aliyuncs.com/images/202207251454488.png)

### 安装 React

由于CCFK组件库只支持React 17, 所以这里我们不能直接安装最新的React 18

运行以下命令安装React v17.0.2

```bash
npm install react@17.0.2 react-dom@17.0.2
```

安装成功后`package.json`中会多出项目依赖

```diff
+  "dependencies": {
+    "react": "^17.0.2",
+    "react-dom": "^17.0.2"
+  }
```



接下来我们把`app.js`里的代码替换成React的JSX语法

1. 在src文件夹中创建一个`index.js`做为入口文件, 并向文件中添加如下代码\

   ```jsx
   // index.js
   import React from "react";
   import ReactDOM from "react-dom";
   import App from "./app";
   
   ReactDOM.render(<App />, document.getElementById("root"));
   ```

2. 修改`src/app.js`中的代码

   ```diff
   - const root = document.getElementById("root");
   - root.innerHTML = "<h1>Hello World</h1>";
   
   + import React from "react";
   +
   + export default function App() {
   +   return <h1>Hello World</h1>;
   + }
   ```

3. 替换`webpack.common.confid.js`中的入口文件

```diff
const path = require('path');

module.exports = {
  entry: {
-   app: './src/app.js',
+   index: './src/index.js',
  },
  output: {
    filename: 'js/bundle.js',
    path: path.resolve(__dirname, '../dist')
  }
}
```

现在运行以下`npm run build`, 会发现报错了, 原因是缺少讲jsx语法转译为js语言的预处理插件, 所以下面配置Babel



### 安装 Babel

运行以下命令安装Babel及其依赖

```bash
npm install --save-dev babel-loader @babel/core @babel/preset-env @babel/preset-react @babel/plugin-proposal-class-properties @babel/plugin-transform-runtime
```

这些依赖各自的作用如下

+ babel-loader：使用 Babel 和 webpack 来转译 JavaScript 文件。
+ @babel/core：babel 的核心模块
+ @babel/preset-env：转译 ES2015+的语法
+ @babel/preset-react：转译 react 的 JSX
+ @babel/plugin-proposal-class-properties：用来编译类(class)
+ @babel/plugin-transform-runtime：防止污染全局，代码复用和减少打包体积

修改`webpack.common.config.js`如下以引入这些Babel

```js
const path = require("path");

module.exports = {
  entry: {
    index: "./src/index.js",
  },
  output: {
    filename: "js/bundle.js",
    path: path.resolve(__dirname, "../dist"),
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /(node_modules|bower_components)/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env", "@babel/preset-react"],
            plugins: [
              "@babel/plugin-transform-runtime",
              "@babel/plugin-proposal-class-properties",
            ],
          },
        },
      },
    ],
  },
};

```

现在再运行`npm run build `就可以成功打包了

![image-20220725151939444](https://pele-images.oss-cn-hangzhou.aliyuncs.com/images/202207251519539.png)



### 安装 Html-Webpack-Plugin

之前我们是手动添加的`public/index.html`文件, 但是在生产中可能会生成多个HTML文件, 所以需要安装`html-webpack-plugin`插件, 让Webpack在打包时自动创建一个HTML文件

运行以下命令安装html-webpack-plugin
```bash
npm install --save-dev html-webpack-plugin
```

修改`webpack.prod.config.js`以调用html-webpack-plugin

```js
const { merge } = require('webpack-merge');
const common = require('./webpack.common.config.js');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = merge(common, {
  mode: 'production',
  plugins: [
    new HtmlWebpackPlugin({
      template: 'public/index.html',
      filename: 'index.html',
      inject: 'body',
      minify: {
        removeComments: true,
      },
    }),
  ],
});
```

解释以下这里的属性

+ template：基于我们自己定义的 html 文件为模板生成 html 文件
+ filename：打包之后的 html 文件名字
+ inject：将 js 文件注入到 body 最底部
+ minify：压缩 html 文件时的配置
  + removeComments：去除注释

还有其他一些属性可以参考 [HTML Webpack Plugin](https://github.com/jantimon/html-webpack-plugin#configuration) 项目文档

修改一下`public/index.js`, 删去引入`../dist/js/bundle.js`的这行

```diff
 <!DOCTYPE html>
 <html lang="en">
   <head>
     <meta charset="UTF-8" />
     <meta name="viewport" content="width=device-width, initial- scale=1.0" />
     <title>react_webpack</title>
   </head>
   <body>
     <div id="root"></div>
   </body>
-   <script src="../dist/js/bundle.js"></script>
 </html>

```

现在再运行`npm run build`, dist文件夹中就会多出一个`index.html`文件

```bash
|- dist
	|- js
	|- index.html
```

```html
<!-- dist/index.html  -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>react_webpack</title>
  </head>
  <body>
    <div id="root"></div>
  <script defer="defer" src="js/bundle.js"></script></body>
  <script src="../dist/js/bundle.js"></script>
</html>
```



#### 修改配置文件以生成不同的HTML文件

如果每次都生成一样的JS文件, 浏览器可能不会发现网页已经更新, 从而直接读取本地的缓存, 所以需要通过修改output中的属性使每次打包后生成不同的JS文件名

只需在`webpack.common.config.js`中添加这段代码

```diff
 module.exports = merge(common, {
+  output: {
+    filename: "js/[name]-bundle-[hash:6].js",
+  },
   mode: "production",
   ...
```

+ name: 打包入口的名称
+ hash:6: 生成6位hash码

此时再运行`npm run build`, 可以发现`dist/js`文件夹中生成了新的js文件

```diff
|- dist
	|- js
		|- bundle.js
+		|- index-bundle-2c81f8.js
	|- index.html
```



#### 安装 Clean Webpack Plugin

经过上面的配置后每次打包都会生成新的文件, 但是我们发现原有的文件却没有被删除, 这样随着打包次数的增加, 文件也会越来越多, 所以需要安装另一个`clean-webpack-plugin`来**在每次打包编译前清理dist目录**

运行以下命令安装`clean-webpack-plugin`

```bash
npm install --save-dev clean-webpack-plugin
```

修改`webpack.prod.config.js`, 引入`clean-webpack-plugin`

```diff
  const HtmlWebpackPlugin = require("html-webpack-plugin");
+ const { CleanWebpackPlugin } = require('clean-webpack-plugin');

  module.exports = merge(common, {
    output: {
      filename: "js/[name]-bundle-[hash:6].js",
    },
    mode: "production",
    plugins: [
+     new CleanWebpackPlugin(),
      new HtmlWebpackPlugin({
      	template: "public/index.html",
      	...
```

现在在打包编译后旧的文件就会被清除了



### 安装 Webpack Dev Server

经过上面的配置, 我们已经可以使用`npm run build`命令来构建并打包React文件到HTML文件中, 但是每次构建完都要手动打开`dist/index.html`文件来查看效果, 所以需要安装`webpack-dev-server`, 使项目webpack打包后能创建一个进程运行生成的文件

运行以下命令安装`webpack-dev-server`

```bash
npm install --save-dev webpack-dev-server
```

配置`webpack.dev.config.js`

```js
const path = require('path');
const { merge } = require('webpack-merge');
const common = require('./webpack.common.config.js');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = merge(common, {
  mode: 'development',
  devServer: {
    static: {
    	directory: path.join(__dirname, 'dist'),
    },
    port: 8080,
    compress: true,
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'public/index.html',
      inject: 'body',
      hash: false,
    }),
  ],
});

```

+ static.directory: 指定启动文件的目录
+ port: 进程运行的端口号
+ compress: 是否开启gzip压缩

修改`package.json`中的指令以调用`webpack-dev-server`

```diff
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
-   "start": "webpack --config ./config/webpack.common.config.js",
+   "start": "webpack serve --open --config ./config/webpack.dev.config.js",
    "build": "webpack --config ./config/webpack.prod.config.js"
  },
```

现在运行`npm run start`, 就可以打包编译并且在本地的8080端口访问生成的页面啦

![image-20220725160514268](https://pele-images.oss-cn-hangzhou.aliyuncs.com/images/202207251605413.png)

##### 参考:

[从零配置webpack 5 + React脚手架（一）](https://juejin.cn/post/6943164629434499109)

[HtmlWebpackPlugin](https://www.webpackjs.com/plugins/html-webpack-plugin/)

[WebpackDevServer](https://webpack.docschina.org/guides/development/#using-webpack-dev-server)

[html-webpack-plugin 使用总结](https://juejin.cn/post/6844903853708541959)
