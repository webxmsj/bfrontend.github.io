---
title: '小技巧写出更好的javascript条件语句'
date: 2018-10-09 20:57:38
tags: [JavaScript]
published: true
hideInList: false
feature: https://imgoss.bfrontend.com/2019-05-31-154914.jpg
---

[原文地址](https://scotch.io/tutorials/5-tips-to-write-better-conditionals-in-javascript)

## 1. 使用`Array.includes`来处理多重条件

使用前：

```
// 条件语句
function test (fruit) {
  if (fruit == 'apple' || fruit == 'redjujube') {
    console.log('red');
  }
}
```

乍一看，这么些似乎没有什么大问题。然而，如果我们想要匹配更多的红色水果呢，比如说[火龙果]和[草莓]？我们是不是得用更多的 `||` 来扩展这条语句？
我们可以使用 `Array.includes` 重写以上条件句。

```
function test (fruit) {
  // 将条件提取到数组中
  const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries']

  if (redFruits.includes(fruit)) {
    console.log('red')
  }
}
```

## 2. 少写嵌套，尽早返回
让我们为之前的例子添加两个条件：
* 如果没有提供水果，抛出错误。
* 如果该水果的数量大于10，将其打印出来

```
function test (fruit, quantity) {
  const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries']

  if (fruit) {
    if (redFruits.includes(fruit)) {
      console.log('red')

      if (quantity > 10) {
        console.log('big quantity')
      }
    }
  } else {
    throw new Error('No fruit')
  }
}
```

让我们回顾上面的代码,我们有：
* 1个if/else语句来筛选无效的条件
* 3层if 语句嵌套


1. 减少嵌套

```
function test (fruit, quantity) {
  const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries']

  if (!fruit) throw new Error('No fruit')

  if (redFruits.includes(fruit)) {
    console.log('red')

    if (quantity > 10) {
      console.log('big quantity')
    }
  }
}
```

// 不推荐使用以下此版本
* 条件反转会导致更多的思考过程(增加认知负担)
2. 进一步减少嵌套

```
function test (fruit, quantity) {
  const redFruits = ['apple', 'strawberry', 'cherry', 'cranberries']

  if (!fruit) throw new Error('No fruit')
  if (!redFruits.includes(fruit)) return ;

  console.log('red')

  if (quantity > 10) {
    console.log('big quantity')
  }
}
```

## 3. 使用函数默认参数和解构
在javascript 中我们经常需要检查`null`/`undefined`并赋予默认值

```
function test(fruit,quantity) {
  if (!fruit) return;
  const q = quantity || 1; // 如果没有提供 quantity, 默认为1
  console.log(`we have ${q} ${fruit}`)
}
```

事实上我们可以通过函数的默认参数来去掉变量 `q`

```
function test(fruit,quantity = 1) {
  if (!fruit) return;
  console.log(`we have ${q} ${fruit}`)
}
```

那么如果`fruit`是一个对象(Object)呢？我们还可以使用默认参数吗？

```
function (fruit) {
  if (fruit && fruit.name) {
    console.log(fruit.name)
  } else {
    console.log('unknown')
  }
}
```

观察上面的例子，当水果名称属性存在时，我们希望将其打印出来，否则打印`unknown`，我们可以通过默认参数和解构赋值的方法来避免写出`fruit && fruit.name` 这种条件

```
function test({name} = {}) {
  console.log(name || 'unknown')
}
```
既然我们只需要fruit的`name`属性,我们可以使用`{name}`来将其解构出来，之后我们就可以在代码中使用`name`变量来取代`fruit.name`

## 4. 相较于switch ,Map/Object 也许是更好的选择

```
function test (color) {
  switch (color) {
    case 'red':
      return ['apple', 'strawberry']
    case 'yellow':
      return ['banana', 'pineapple']
    case 'purple':
      return ['grape', 'plum']
    default:
      return []
  }
}
```
使用对象字面量来进行优化

```
const fruitColor = {
  red: ['apple', 'strawberry'],
  yellow: ['banana', 'pineapple'],
  purple: ['grape', 'plum']
}

function test (color) {
  return fruitColor[color] || []
}
```

或者使用 `Map` 来实现同样的效果

```
const fruitColor = new Map()
    .set('red', ['apple', 'strawberry'])
    .set('yellow', ['banana', 'pineapple'])
    .set('purple', ['grape', 'plum'])
function test (color) {
  return fruitColor.get(color) || []
}
```

或者 使用 `Array.filter` 实现同样的效果

```
const fruits = [{
  name: 'apple',
  color: 'red'
}, {
  name: 'strawberry',
  color: 'red'
}, {
  name: 'banana',
  color: 'yellow'
}]
function test (color) {
  return fruits.filter(f => f.color == color)
}
```

## 5. 使用 Array.every 和 Array.some 来处理全部/部分满足条件

```
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
  ];

function test() {
  let isAllRed = true;

  // 条件：所有的水果都必须是红色
  for (let f of fruits) {
    if (!isAllRed) break;
    isAllRed = (f.color == 'red');
  }

  console.log(isAllRed); // false
}
```
使用 `Array.every` 来缩减代码

```
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
  ];

function test() {
  // 条件：（简短形式）所有的水果都必须是红色
  const isAllRed = fruits.every(f => f.color == 'red');

  console.log(isAllRed); // false
}
```

如果我们想要检查是否有至少一个水果是红色的，我们可以使用 `Array.some` 

```
const fruits = [
    { name: 'apple', color: 'red' },
    { name: 'banana', color: 'yellow' },
    { name: 'grape', color: 'purple' }
];

function test() {
  // 条件：至少一个水果是红色的
  const isAnyRed = fruits.some(f => f.color == 'red');

  console.log(isAnyRed); // true
}

```