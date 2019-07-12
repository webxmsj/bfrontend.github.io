---
title: 《深入浅出webpack》 构建工具
date: 2019-07-12 13:07:26
tags: 
  - JavaScript
  - 深入浅出webpack
  - scss
categories:
  - 深入浅出webpack-笔记
feature: 
---

## 《深入浅出webpack》 读书笔记-002



作为一名前端工程师 ，不禁感叹前端技术发展之快，各种可以提高开发效率额新思想和新框架层出不穷，但是他们都有一个共同点：源代码无法直接运行，必须通过转换后才能正常运行。



<!-- more -->



### 前端构建工具及对比

> 构建是将源代码转换成可执行的JavaScript、css、html代码，以及以下内容
>
>  * 代码转换： 将typescript编译成JavaScript、将scss编译成css等。
>  * 文件优化： 压缩JavaScript、css、html代码，压缩合并图片等。
>  * 代码分割： 提取多个页面的公共代码，提取首屏不需要执行部分的代码让其异步加载。
>  * 模块合并：在采用模块化的项目中，需要通过构建功能将模块分类合并成一个文件。
>  * 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
>  * 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。以及相关覆盖率是否达标。
>  * 自动发布：更新代码后，自动构建出线上发布代码并传输给发布系统。
>
> 构建其实是工程化、自动化思想在前端开发中的一种体现，将一系列流程用代码去实现，让代码自动化执行这一系列复杂的流程。构建为前端开发注入了更大的活力，解放了我们的生产力。



#### 1. npm script

npm是按照node时附带的包管理器，`npm script`是npm 内置的一个功能，允许在package.json文件里面使用`script`字段定义任务。

![carbon](https://imgoss.bfrontend.com/2019-07-12-043329.png)




<details>
<summary>代码</summary>

```json
{
  "script": {
    "dev": "node dev-server.js",
    "build": "node build.js"
  }
}
```
</details>

里面的scripts字段是一个对象，每个属性对应一段`shell`脚本，以上代码定义了两个任务: dev 和 build。其底层实现原理是通过调用shell去运行脚本命令，例如： 运行`npm run dev`等同于执行`node dev-server.js`命令

优点：

```markdown
* 无需安装其他依赖。
```

缺点:

```markdown
* 功能上有些简单，虽然提供了pre和post两个钩子，但不能方便地管理多个任务之间的依赖。
```

#### 2. Grunt

> [Grunt](https://gruntjs.com)和npm script类似，也是一个任务执行者。 `Grunt`有大量现成的插件封装了常见的任务，也能管理任务之间的依赖关系，自动化地执行依赖的任务。



![carbon](https://imgoss.bfrontend.com/2019-07-12-043436.png)



<details>
<summary>代码</summary>

```js
module.exports = function(grunt) {
  grunt.initConfig({
    // uglify 插件的配置信息
    uglify: {
      app_task: {
        files: {
          'build/app.min.js': ['lib/index.js', 'lib/test.js']
        }
      }
    },
    // watch 插件配置信息
    watch: {
      another: {
        files: ['lib/*.js']
      }
    }
  });
  // 使用插件定义
  grunt.loadNpmTasks('grunt-contrib-uglify')
  grunt.loadNpmTasks('grunt-contrib-watch')
  // 执行任务定义
  grunt.registerTask('dev', ['uglify', 'watch'])
}
// 项目根目录下，执行grunt dev命令就会启动JavaScript文件压缩和自动刷新功能
```
</details>


优点:
```markdown
* 灵活，它只负责执行我们执行的任务
* 大量的可复用插件封装好了常见的构建任务。
```

缺点:
```markdown
* 集成度不高，要写很多配置后才可以使用，无法做到开箱即用
```

#### 3. Gulp

> [Gulp](http://gulpjs.com)是一个基于流的自动化构建工具。除了可以管理和执行任务，还可支持监听文件、读写文件。gulp常用的5中方法

```markdown
* 通过`gulp.task`注册一个任务
* 通过`gulp.run`执行任务
* 通过`gulp.watch`监听文件的变化
* 通过`gulp.src`读取文件
* 通过`gulp.dest`写文件
```

Gulp的最大特点就是引入了流的概念，同时提供了一系列常用的插件去处理流，流可以在插件之间传递，示例代码如下：

![carbon](https://imgoss.bfrontend.com/2019-07-12-043539.png)

<details>
<summary>代码</summary>

```js
// 引入 Gulp
var gulp = require('gulp')
// 引入插件
var jshint = require('gulp-jshint')
var sass = require('gulp-sass')
var concat = require('gulp-concat')
var uglify = require('gulp-uglify')
// 编译sass 任务
gulp.task('sass', function() {
  // 通过src读取文件， 通过管道给到插件
  gulp.src('./scss/*.scss')
  		// 将scss转换成css文件
  		.pipe(sass())
  		// 输出文件
  		.pipe(gulp.dest('./css'))
})

```
</details>

优点：

```markdown
* 好用、灵活、既可以单独完成构建，也可以和其他工具搭配使用。
```

缺点：

```markdown
* 类似于Grunt, 集成度不高潮，要写很多配置后才可以使用，无法做到开箱即用。
```

#### 4. Fis3

> [Fis3](http://fis.baidu.com/)是一个来自百度的优秀国产构建工具。相对于Grunt、Gulp这些只提供了基本功能的工具，Fis3集成了web开发中的常用构建功能，如下所述。

* 读写文件： 通过fis.match读文件，release配置文件的输出路径。

* 资源定位：解析文件之间的依赖关系和文件位置。

* 文件指纹： 在通过useHash配置输出文件时为文件URI加上md5戳，来优化浏览器的缓存。

* 文件编译：通过parser配置文件解析器做文件转换，例如将ES6转换成ES5。

* 压缩资源： 通过optimizer配置代码压缩方法。

* 图片合并： 通过spriter配置合并css里导入的图片到一个文件中，来减少HTTP请求数。

![carbon](https://imgoss.bfrontend.com/2019-07-12-043618.png)

<details>
<summary>代码</summary>

```js
// 加 md5 
fis.match('*.{js,css,png}', {
	useHash: true
});
// 通过 fis3-parser-typescript 插件可将 Typescript 文件转换成 JavaScript 文件 
fis.match ('*.ts', {
	parser: fis.plugin ('typescript') 
});
// 对 css 进行雪碧图合并 
fis.match ('* .css', {
//为匹配到的文件分配属性 useSprite 
  useSprite: true
});
//压缩 JavaScript 
fis .match('*.js', {
	optimizer: fis .plugin ('uglify-js') 
});
//压缩 css
fis.match('*.css '，{
	optimizer: fis.plugin ('clean-css') 
});
//压缩图片
fis.match('*.png', {
	optimizer: fis.plugin ('png- compressor') 
});
```
</details>

优点：

```markdown
* 集成了各种web开发所需的构建功能，配置简单、开箱即用。
```

#### 5. webpack

> [webpack](https://www.webpackjs.com/)是一个打包模块化JavaScript的工具，在`webpack`里一切文件皆模块，通过`Loader`转换文件，通过`plugin`注入钩子，最后输出由多个模块组合成的文件。`webpack`专注于构建模块化项目。


![carbon](https://imgoss.bfrontend.com/2019-07-12-043651.png)

<details>
<summary>代码</summary>

```js
module.exports = {
  // 入口
  entry: './app.js',
  output: {
    // 将入口文件所依赖的所有模块打包成一个文件bundle.js输出
    filename: 'bundle.js'
  }
}
```
</details>

优点：

```markdown
* 专注于处理模块化的项目能做到开箱即用、一步到位
* 可通过plugin扩展，完整好用又不失灵活
* 使用场景不局限于web开发
* 社区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展。
* 良好的开发体验
```

> 大多数团队在开发新项目时会采用紧跟时代的技术，这些技术几乎都会采用“模块 + 新语言 + 新框架”，webpack可以为这些新项目提供一站式的解决方案。
>
> webpack有良好的生态链和维护团队，能提供良好的开发体验并保证质量。
>
> webpack被全世界大量的web开发者使用和验证，能找到各个层面所需的教程和经验分享。

#### 6. Rollup

> [Rollup](https://rollupjs.org)是一个JavaScript模块打包器，可以将小块代码编译成大块复杂的代码，例如library或应用程序。它的亮点在于，能针对ES6源码进行`Tree Shaking`, 以去除哪些已被定义但没被使用的代码并进行`Scope Hoisting`, 以减小输出文件的大小和提升运行性能。

优点：

```markdown
* 打包出来的代码中没有`webpack`那段模块的加载、执行和缓存的代码
* 在用于打包JavaScript库时比webpack更有优势，因为其打包出来的代码更小、更快。
```

![carbon](https://imgoss.bfrontend.com/2019-07-12-050246.png)

<details>
<summary>代码</summary>

```js
// rollup.config.js 配置类似于webpack
export default {
  // 入口
  input: 'src/main.js',
  output: {
    // 将入口文件所依赖的所有模块打包成一个文件bundle.js输出
    file: 'bundle.js'
  }
}
```
</details>


#### 7. Parcel

> [Parcel](https://parceljs.org/)是web应用打包工具，适用于经验不同的开发者。它利用多核处理提供了极快的速度，并且不需要任何配置。



#### 8. browserify

> [browserify](http://browserify.org/)可以让你使用类似于 node 的 require() 的方式来组织浏览器端的 Javascript 代码，通过 [预编译](https://link.jianshu.com/?t=https://baike.baidu.com/item/预编译/3191547) 让前端 Javascript 可以直接使用 Node NPM 安装的一些库。
