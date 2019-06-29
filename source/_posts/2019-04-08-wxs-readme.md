---
title: '微信小程序wxs中可用的数据类型及方法'
date: 2019-04-08 20:57:38
tags: [小程序,wxs,js]
published: true
hideInList: false
feature: http://imgoss.bfrontend.com/2019-05-31-154300.jpg
---


## 微信小程序 `wxs` 可用的`数据类型`及`方法`

> 小程序中目前共有以下几种数据类型
* number : 数值
* string : 字符串
* boolean : 布尔值
* object : 对象
* function : 函数
* array : 数组
* date : 日期
* regexp : 正则

### 1. Number
> 语法
number 目前包含两种数值： 整数、小数

```
var a = 10
var PI = 3.141592653589793
```

> 属性
* constructor : 返回字符串 'Number'

> 方法
* toString
* toLocaleString
* valueOf
* toFixed
* toExponential
* toPrecision

### 2. String
> 语法
string 的定义方式
```
var a = 'hello world'
var b = "hello world"
```

> 属性
* constructor 返回字符串'String'
* length
> 方法
* toString
* valueOf
* charAt
* charCodeAt
* concat
* indexOf
* lastIndexOf
* localeCompare
* match
* replace
* search
* slice
* split
* substring
* toLowerCase
* toLocaleLowerCase
* toUpperCase
* toLocaleUpperCase
* trim

### 3. boolean
> 语法
object 是一种无序的键值对。
使用方式如下：
```
// 生成一个新的空对象
var o = {}
// 生成一个新的非空对象
o = {
  'string': 1,
  const_var: 2,
  func: {}
}
// 对象属性的写操作
o['string']++
o['string'] += 10
o.const_var++
o.const_var+=10
// 对象属性的读操作
console.log(o['string'])
console.log(o.const_var)
```
> 属性
* constructor: 返回字符串 'Object'
```
console.log('Object' === {name:'good',age:12}.constructor)
```
> 方法
* toString: 返回字符串'[object Object]'

### 4. function
> 语法
function 支持如下定义方式
```
// 方式一
function a(x) {
  return x
}
// 方式二
var b = function(x) {
  return x
}
```
function 同时也支持一下的语法(匿名函数,闭包等)
```
var a = function(x) {
  return function() { return x }
}
var b = a(100)
console.log(100 === b())
```
> function 里面可以使用arguments 关键词。 该关键词目前只支持一下属性
* length 传递给函数的参数个数
* [index] 通过index下标可以遍历传递给函数的每个参数
示例代码如下:
```
var a = function() {
  console.log(3 === arguments.length)
  console.log(100 === arguments[0])
}
```
> 属性
* constructor 返回字符串'[function Function]'
* length 返回函数的形参个数
> 方法
* toString 返回字符串'[function Function]'
示例代码
```
var func = function(a, b, c) {}
console.log('Function' === func.constructor)
console.log(3 === func.length)
console.log('[function Function]' === func.toString())
```

### 5. array
> 语法
array 支持以下的定义方式
```
var a = [] // 定义一个空数组
a = [1, '2', {}, function(){}] // 生成一个新的非空数组
```
> 属性
* constructor 返回字符串'Array'
* length
> 方法
* toString
* concat
* join
* pop
* push
* reverse
* shift
* slice
* sort
* splice
* unshift
* indexOf
* lastIndexOf
* every
* some
* forEach
* map
* filter
* reduce
* reduceRight
### 6. date
> 语法
生成date对象需要使用getDate函数,返回一个当前时间的对象
```
getDate()
getDate(milliseconds)
getDate(datestring)
getDate(year, month[, date[, hours[, minutes[, seconds[, milliseconds]]]]])
```
* 参数
  * milliseconds: 从1970年1月1日00:00:00 UTC开始计算的毫秒数
  * datestring: 日期字符串, 其格式为: 'month data, year hours:minutes:seconds'

示例代码
```
var date = getDate()
date = getDate(1500000000000)
date = getDate('2017-7-4')
date = getDate(2017, 6, 14, 10, 40, 0, 0)
```
> 属性
constructor: 返回字符串'Date'
方法
* toString
* toDateString
* toTmeString
* toLocaleString
* toLocaleDateString
* toLocaleTimeString
* valueOf
* getTime
* getFullYear
* getUTCFullYear
* getMonth
* getUTCMonth
* getDate
* getUTCDate
* getDay
* getUTCDay
* getHours
* getUTCHours
* getMinutes
* getUTCMinutes
* getSeconds
* getUTCSeconds
* getMilliseconds
* getUTCMilliseconds
* getTimezoneOffset
* setTime
* setMilliseconds
* setUTCMilliseconds
* setSeconds
* setUTCSeconds
* setMinutes
* setUTCMinutes
* setHours
* setUTCHours
* setDate
* setUTCDate
* setMonth
* setUTCMonth
* setFullYear
* setUTCFullYear
* toUTCString
* toISOString
* toJson

### 7. regexp
> 语法
生成regexp 对象需要使用getRegExp函数
```
getRegExp(pattern[, flags])
```
* 参数：
  * pattern: 正则表达式的内容
  * flags: 修饰符 该字段只能包含以下字符串
    * g: global
    * i: ignoreCase
    * m: multiline
示例代码
```
var a = getRegExp('x', 'img')
console.log('x' === a.source)
console.log(true === a.global)
console.log(true === a.ignoreCase)
console.log(true === a.multiline)
```
> 属性
* constructor 返回字符串'RegExp'
* source
* global
* ignoreCase
* multiline
* lastIndex
> 方法
* exec
* test
* toString

### 8. 数据类型判断
1. 数据类型的判断可以使用constructor属性

```
function is(obj) { return obj.constructor }
var number = 10
is(number) // 'Number'
var string = '123'
is(string) // 'String'
```

2. 使用typeof 也可以区分部分数据类型

```
function is(obj) { return typeof obj }
var number = 10
is(number) // 'number'
var boolean = true
is(boolean) // 'boolean'
```
