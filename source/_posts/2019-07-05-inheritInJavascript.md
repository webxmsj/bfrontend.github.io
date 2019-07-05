---
title: 聊聊js中的继承(一个简单的造人项目)
date: 2019-07-05 16:50:33
tags: 
  - javascript
  - 继承
  - inherit
categories:
  - javascript
published: true
---

## 聊聊js中的继承(一个简单的造人项目)

> 我们有了大批廉价的妖精

### 妖精转人类实验一

``` javascript
// 创建一个人的类型
function Person() {}
Person.prototype.think = function(){ console.log('How to Make More Money') }

// 妖精 
function Fairy() {};

// 妖精一族想要成为人类,我们尝试给他加上能够思考的能力
Fairy.prototype.think = Person.prototype.think;

// 妖精中的一只狐妖
var foxFairy = new Fairy()

// 我们看看它是个人吗？
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // false
```

> :thinking:  我们尝试将Person原型(prototype)上的think属性方法复制到Fairy的原型上作为同名方法来实现继承功能。
>
>😅  测试结果表明，尽管我们可能已经教会了妖精思考，但是没能让妖精们成为一个人(Person)。这不是继承而是复制
>
>我们尝试其他方案

### 妖精转人类实验二(原型链继承)

> 我们要想让妖精成为一个人，看来只能是让妖精的原型(prototype)等于一个人，这样就会形成一个链式的结构。
>
> 我们尝试修改下我们的代码

```javascript
// 创建一个人的类型
function Person() {}
Person.prototype.think = function(){ console.log('How to Make More Money') }

// 妖精 
function Fairy() {};

// 妖精一族想要成为人类,我们尝试给他加上能够思考的能力
** Fairy.prototype = new Person() **

// 妖精中的一只狐妖
var foxFairy = new Fairy()

// 我们看看它是个人吗？
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // true
```

> ⚠️  大家可能会想起另外一个技巧，但强烈建议不要使用, 那就是将`Fairy.prototype = new Person()`改为`Fairy.prototype = Person.prototype`，这样做的话，`Fairy`原型上的任何修改都会影响到`Person`的原型，因为它们是引用类型，指向的是堆内存中同一个对象，这样做肯定会有不良的副作用。
>
>  :thinking:  虽然我们实现了将妖精进化成人类这么一个操作，但是呢？每一个妖精都没有特色了，想给自己起个名字都不可以吗？

### 妖精转人类实验三(借用构造函数继承)

> 我们既要让妖精进化为一个人类，又要让其能有自己的特色，比如名字。
>
> 我们想到了借用构造函数，修改代码如下：

```javascript
// 创建一个人的类型
function Person(name) {
  this.name = name
}
Person.prototype.think = function(){ console.log('How to Make More Money') }

function Fairy(name) {
  Person.call(this, name)
};

// 妖精中的一只狐妖
var foxFairy = new Fairy('xiaoyao')

// 我们看看它是个人吗？
assert(foxFairy.name === 'xiaoyao', 'foxFairy should has a name "xiaoyao"')  // true
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // false
```

> :thinking:  虽然每个妖精有了自己特色的名字，但是缺乏了人类公共的一些功能如思考，算不上一个人。

### 妖精转人类实验四(组合继承)

```javascript
// 创建一个人的类型
function Person(name) {
  this.name = name
}
Person.prototype.think = function(){ console.log('How to Make More Money') }

function Fairy(name) {
  Person.call(this, name)
};
// 具备人类公共的功能
Fairy.prototype.think = new Person();

// 妖精中的一只狐妖
var foxFairy = new Fairy('xiaoyao')

// 我们看看它是个人吗？
assert(foxFairy.name === 'xiaoyao', 'foxFairy should has a name "xiaoyao"')  // true
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // false
```

> 既有人类思考的能力，也有自己特色的名字了, 但是调用了两次`Person`构造函数(耗内存)
>
> 换种思路，我们对人类进行仿制

### 妖精转人类实验五(原型式继承)

```javascript
// 创建一个人的类型
function Person(name) {
  this.name = name
}
Person.prototype.think = function(){ console.log('How to Make More Money') }

// 建立一个山寨工厂，用来仿制传入的东西
function imitationUtil(obj) {
  function F() {};
  F.prototype = obj;
  return new F();
}

var man = new Person()
// 我们依照男人(man),进行一个仿制看看得到了一个什么？
var something = imitationUtil(man)
assert(foxFairy.name === 'xiaoyao', 'foxFairy should has a name "xiaoyao"')  // false
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // true
```

> 🔑 我们仿制出来的这个东西已经是一个人了，但是缺乏其独自的特色

### 妖精转人类实验六(寄生式继承)

```javascript
// 创建一个人的类型
function Person(name) {
  this.name = name
}
Person.prototype.think = function(){ console.log('How to Make More Money') }

// 建立一个山寨工厂，用来仿制传入的东西
function imitationUtil(obj) {
  function F() {};
  F.prototype = obj;
  return new F();
}

function Fairy(obj, name) {
  var something = imitationUtil(obj)
  something.name = name
  return something;
};

var man = new Person()

// 妖精中的一只狐妖
var foxFairy = Fairy(man, 'xiaoyao')

assert(foxFairy.name === 'xiaoyao', 'foxFairy should has a name "xiaoyao"')  // true
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // true
```

### 妖精转人类实验七(寄生组合式继承)

```javascript
// 创建一个人的类型
function Person(name) {
  this.name = name
}
Person.prototype.think = function(){ console.log('How to Make More Money') }

// 建立一个山寨工厂，用来仿制传入的东西
function imitationUtil(obj) {
  function F() {};
  F.prototype = obj;
  return new F();
}

var semiFinished = imitationUtil(Person.prototype)

function Fairy(name) {
  Person.call(this, name)
}

Fairy.prototype = semiFinished
semiFinished.constructor = Fairy

// 妖精中的一只狐妖
var foxFairy = new Fairy('xiaoyao')

assert(foxFairy.name === 'xiaoyao', 'foxFairy should has a name "xiaoyao"')  // true
assert(foxFairy instanceof Person, 'foxFairy should be a person')  // true

```

### 寄生式继承与寄生组合式继承区别

> 寄生式继承
>
> 在第一次调用Person构造函数时，Fairy.prototype会得到name属性(Person的实例属性), 只不过现在位于Fairy的原型中。
>
> 当调用Fairy构造函数时，又在新对象上创建了实例属性name
>
> 在获取name 属性时，实例上的name属性就会屏蔽原型中的同名属性

|                          寄生式继承                          |                        寄生组合式继承                        |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![image-20190705163750557](http://imgoss.bfrontend.com/2019-07-05-083750.png) | ![image-20190705163842305](http://imgoss.bfrontend.com/2019-07-05-083842.png) |
