---
title: Effective javascript 读书笔记
date: 2018-09-29 20:57:38
tags: [阅读,学习,高阶函数]
published: true
hideInList: false
feature: 
---

## 高阶函数

高阶函数过去曾经是函数式编程的一句行话，似乎也是一种先进的编程技术的一个深奥术语。
高阶函数无非是那些将函数作为参数或返回值的函数。将函数作为参数(通常称为回调函数，因为高阶函数“随后调用”)是一种特别强大、富有表现力的惯用法，也在`javascript`程序中被大量使用。

* 高阶函数是那些将函数作为参数或返回值的函数。
* 熟悉掌握现有库中的高阶函数。
* 学会发现可以被高阶函数所取代的常见的编码模式。

### 1. 迭代器
```
  var it = values(1,3,2,45,676,77);
  it.next(); // 1
  it.next(); // 3
  it.next() // 2

  function values() {
    var i = 0, n = arguments.length, a = arguments;
    return {
      hasNext: function () {
        return i < n;
      },
      next: function () {
        if (i >= n) {
          throw new Error("end of iteration")
        }
        return a[i++]
      }
    }
  }

```

### 2. 不要信赖函数对象的`toString`方法
JavaScript 函数有一个非凡的特性，即将其源代码重现为字符串的能力。
```
(function (x) {
  return x + 1;
}).toString(); // "function (x) {\n return x + 1; \n}"
```
反射获取函数源代码的功能很强大，聪明的黑客偶然会通过巧妙地方法用到它。但是使用函数对象的toString方法有严重的局限性。
首先，ECMAScript 标准对函数对象的toString方法的返回结果(即该字符串)并没有任何要求。这意味着不同的javascript引擎将产生不同的字符串，甚至产生的字符串与该函数并不相关。
事实上，如果函数是使用纯javascript实现的，那么javascript引擎会试图提供该函数的源代码的真实表示。下面是一个失败的例子。该例子失败的原因是使用了由宿主环境的内置库提供的函数。

```
(function (x) {
  return x + 1;
}).bind(16).toString(); // "function (x) {\n [native code]\n}"
```

首先，由于在许多宿主环境中，bind函数是由其它编程语言实现的(通常是c++)。宿主环境提供的是一个编译后的函数，在此环境下该函数没有javascript的源代码供显示。
其次，由于标准允许浏览器引擎改变toString方法的输出，这就很容易使编写的程序在一个javascript系统中正确运行，在其他javascript系统中却无法正确运行。程序对函数的源代码字符串的具体细节很敏感，即使javascript的实现有一点细微的变化(如空格格式化)都可能破坏程序。

最后,由toString方法生成的源代码并不展示闭包中保存的与内部变量引用相关的值。

```
(function (x) {
  return function (y) {
    return x + y;
  }
})(42).toString(); // "function (y) {\n return x + y; \n}"
```

注意，尽管函数实际上是一个绑定x为42的闭包，但结果字符串仍然包含一个引用了x的变量。
从某种意义上说，toString方法的这些局限使其用来提取函数源代码并不是特别有用和值得信赖。通常应该避免使用它。对提取函数源代码相当复杂的使用应当采用精心制作的javascript解释器和处理库。但毫无疑问，将javascript函数看作要给不该违背的抽象是最稳妥的。

* 当调用函数的toString方法时，并没有要求javascript引擎能够精确地获取到函数的源代码。
* 由于在不同的引擎下调用toString方法的结果可能不同，所以绝不要信赖函数源代码的详细细节。
* toString方法的执行结果并不会暴露存储在闭包中的局部变量值。
* 通常情况下，应该避免使用函数对象的toString方法。

### 3. 理解`prototype`、`getPrototypeOf`和`__proto__`之间的不同

在许多语言中，每个对象是相关类的实例，该类提供在其所有实例间共享代码。相反javascript并没有类的内置概念，对象是重其他对象中继承而来。每个对象与其他一些对象是相关的，这些对象称为它的原型。尽管依然使用了很多传统的面向对象语言的概念，但使用原型与使用类有很大差异。

原型包括三个独立但相关的放问题。这三个访问器的命名都是对单词`prototype`做了一些变化。这个不幸的重叠自然会导致相当多的混乱。

* C.prototype用于建立由new C()创建的对象的原型。
* Object.getPrototypeOf(obj) 是ES5中用来获取obj对象的原型对象的标准方法
* Obj.__proto__ 是获取obj对象的原型对象的非标准方法

### 4. 使构造函数与new操作符无关

```
function User(name, passwordHash) {
  this.name = name;
  this.passwordHash = passwordHash;
}
```
当使用如下方法创建一个构造函数时，程序依赖于调用者是否记得使用new操作符来调用该构造函数。

```
var user = new User('xiaohong', 'xiaohongpassword')
```

如果调用者忘记使用new关键字，那么函数的接收者僵尸全局对象

```
var u = User("xiaoli", "rtyui4548asd2f1asd5f4as5")
u; // undefined
this.name; // "xiaoli"
this.passwordHash; //"rtyui4548asd2f1asd5f4as5"
```

该函数不但会返回无意义的undefined,而且会灾难性地创建(如果这些全局变量已经存在则会修改)全局变量name和passwordHash
如果将User函数定义为ES5的严格代码，那么它的接收者默认是undefined;
```
function User(name, passwordHash) {
  "use strict";
  this.name = name;
  this.passwordHash = passwordHash;
}
var u = User("name","passwordHash");
// error: this is undefined
```

使用如下方式，不管是函数的方式还是以构造函数的方式调用User函数，它都返回一个继承自User.prototype的对象

```
function User(name, passwordHash) {
  if (!(this instanceof User)) {
    return new User(name, passwordHash)
  }
  this.name = name;
  this.passwordHash = passwordHash
}
// 这种模式的一个特点是它需要额外的函数调用，因此代价有点高。而且它也很难适用于可变参数函数
<!-- 另一种方式 -->
function User (name, passwordHash) {
  var self = this.instanceof User
              ? this
              : Object.create(User.prototype);
  self.name = name;
  self.passwordHash = passwordHash
  return self;
}
```