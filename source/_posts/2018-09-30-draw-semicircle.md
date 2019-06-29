---
title: div + css 绘制半圆(黑色 + 白色)
date: 2018-09-30 20:57:38
tags: [技术]
published: true
hideInList: false
feature: 
---

## 一个div，写css把它变成一个圆，上半部分是黑色，下部分是白色

<style>
.semicircle1{
  width: 100px;
  height: 50px;
  border: solid #000;
  background: #fff;
  border-width: 1px 1px 50px 1px;
  border-radius: 50%;
  box-sizing: content-box;
}
</style>
<div class="semicircle1"></div>

> 实现代码如下

```
<style>
.semicircle{
  width: 100px;
  height: 50px;
  border: solid black;
  background: #fff;
  border-width: 1px 1px 50px 1px;
  border-radius: 50%;
}
</style>
<div class="semicircle"></div>
```
## 一个div，实现一个`太极`图
<style>
.taijicontent{
  background: #eee;
  padding: 10px;
  width: 120px;
  box-sizing: content-box;
}
.taiji{
  width: 0px;
  height: 120px;
  border-left: 60px solid #000;
  border-right: 60px solid #fff;
  border-radius: 100%;
  box-sizing: content-box;
}
.taiji:before{
  content: '';
  display: block;
  width: 20px;
  height: 20px;
  border: 20px solid #000;
  border-radius: 100%;
  background: #fff;
  margin-left: -30px;
  box-sizing: content-box;
}
.taiji:after{
  content: '';
  display: block;
  width: 20px;
  height: 20px;
  border: 20px solid white;
  border-radius: 100%;
  background-color: black;
  margin-left: -30px;
  box-sizing: content-box;
}
</style>
<div class="taijicontent">
  <div class="taiji"></div>
</div>
> 实现代码如下

```
<style>
.taiji{
  width: 0px;
  height: 120px;
  border-left: 60px solid #000;
  border-right: 60px solid #fff;
  border-radius: 100%;
}
.taiji:before{
  content: '';
  display: block;
  width: 20px;
  height: 20px;
  border: 20px solid #000;
  border-radius: 100%;
  background: #fff;
  margin-left: -30px;
}
.taiji:after{
  content: '';
  display: block;
  width: 20px;
  height: 20px;
  border: 20px solid white;
  border-radius: 100%;
  background-color: black;
  margin-left: -30px;
}
</style>
<div class="taiji"></div>
```