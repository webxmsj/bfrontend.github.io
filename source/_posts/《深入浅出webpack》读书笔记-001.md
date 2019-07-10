---
title: 《深入浅出webpack》 模块规范
date: 2019-07-10 11:59:26
tags: 
  - JavaScript
  - 深入浅出webpack
  - scss
categories:
  - 深入浅出webpack-笔记
feature: 
---

近年来 Web 应用变得更加复杂与庞大， `Web`前端技术的应用范围也更加广泛。从复杂、庞大的管理后台，到对性能要求苛刻的移动网页，再到类似于 ReactNative 的原生应用开发方案， `Web`前端工程师在面临更多机遇的同时也面临更大的挑战 。通过直接编写 JavaScript、css、 HTML 开发 Web 应用的方式己经无法应对当前 Web 应用的发展。 近年来，前端社区涌现出许多新思想,例如模块化的规范。

<!-- more -->


## 《深入浅出webpack》 读书笔记-001

### 模块化

> 模块化是指将一个复杂的系统分解为多个模块以方便编码。

最开始，开发网页要通过命名空间的方式来组织代码。例如jQuery是通过将它的API都放到window.$下,这样做存在很多问题包括： 

	* 命名空间冲突，两个库可能会使用同一个名称，例如Zepto也是放在window.$下。
	* 无法合理地管理项目的依赖和版本。
	* 无法方便地控制依赖的加载顺序。

#### CommonJS

[Commonjs](http://www.commonjs.org/) 是一个被广泛使用的JavaScript模块化规范，其核心思想是通过require方法来同步加载依赖的其他模块，通过module.exports导出需要暴露的接口。CommonJS规范的流行得益于Node.js采用了这种方式，后来这种方式被引入到了网页开发中。

代码示例如下：

```js
// a.js
const a = function() {
	console.log('this is module a')
}
module.exports = a
// b.js
const A = require('./a')
A() // this is module a
```

优点： 

 * 代码可复用与Node.js环境下进行，例如做同构应用；
 * 通过NPM发布的很多第三方模块都采用了CommonJS规范。

缺点：

	* 无法直接运行与浏览器环境中，需要通过工具转换成标准的ES5

#### AMD

> [AMD](https://en.wikipedia.org/wiki/Asynchronous_module_definition)是也是一种JavaScript模块化规范，与`CommonJS`最大的不同在于，它采用了异步的方式去加载依赖的模块。AMD规范主要用于解决针对浏览器环境的模块化问题，最具代表性的实现是requirejs。

代码示例如下：

```js
// 定义一个模块
define('module', ['deep'], function(dep){
  return exports;
})
// 导入和使用
require(['module'], function(module) {
  console.log(module)
})
```

优点：

	* 可以在不转换代码的情况下直接运行在浏览器中
	* 可异步加载依赖
	* 可并行加载多个依赖
	* 代码可运行在浏览器环境和Node.js环境中

缺点：

	* JavaScript运行环境没有原生支持AMD，需要先导入实现了AMD的库后才能正常使用

#### ES6模块化

> ES6模块化是国际标准化组织ECMA提出的JavaScript模块化规范，它在语言层面上实现了模块化。浏览器厂商和Node.js都宣布要原生支持该规范。

代码示例如下：

```js
// 定义
export const a = function() {
  console.log('this is fn a')
}
export default {
  c,
  b
}
// 导入
import { readFile } from 'fs'
import React from 'react'
```

缺点： 

	* 无法直接运行在大部分JavaScript运行环境中，必须通过工具转换成标准的ES5后才能正常运行。

#### 样式文件中的模块化

以scss为例

代码示例如下：

```scss
// util.scss 文件
@mixin center{
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
// main.scss 文件
@import 'util';
#box{
  @include center;
}
```

