---
title: css水平垂直居中
date: 2018-09-18 20:57:38
tags: 
  - css
  - center
categories:
  - CSS
published: true
feature: 
---

### css 实现水平垂直居中的多种方式

> 这是一道面试必考题，很多面试官都喜欢问这个问题，要想实现这种效果看似简单，实则暗藏玄机。

为了实现如下效果先来做些准备工作,假设HTML代码如下：

```
<div class="wp">
    <div class="box size"></div>
</div>
```
CSS 代码如下： 

```
.wp{
    border: 1px solid red;
    width: 300px;
    height: 300px;
}
.box{
    background: green;
}
.box.size{
    width: 100px;
    height: 100px;
}
```
.size 表示居中元素是否定宽, 下面所有效果都会用到如上代码，主要是设置颜色和宽高


#### 1. 仅居中元素 `定宽高` 适用
* absolute + 负margin
* absolute + margin auto
* absolute + calc (兼容问题)

##### 1.1 absolute + 负margin
绝对定位的百分比是相对于父元素的宽高
```
.wp{
    position:relative;
}
.box{
    position: absolute;
    top: 50%;
    left: 50%;
    margin-left: -50px;
    margin-top: -50px;
}
```
##### 1.2 absolute + margin auto
```
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
```
##### 1.3 absolute + calc
感谢css3带来了计算属性，既然top的百分比是基于元素的左上角，那么 在减去宽度的一半就好了
这种方法兼容性依赖`calc`的兼容性
```
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: calc(50% - 50px);
    left: calc(50% - 50px);
}
```
#### 2. 居中元素 `不定宽高`
* absolute + transform
* lineheight
* writing-mode
* table
* css-table
* flex
* grid

##### 2.1 absolute + transform
修复绝对定位的问题，还可以使用css3新增的transform,transform的translate属性也可以设置百分比，其实相对于自身的宽和高，所以可以将translate设置为-50%就可以做到居中了

```
.wp{
    position: relative;
}
.box{
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
}
```
##### 2.2 lineheight
将box设置为行内元素，通过`text-align`就可以做到水平居中,通过`vertical-align`就可以实现垂直居中

```
.wp{
    line-height: 300px;
    text-align: center;
    font-size: 0px;
}
.box{
    font-size: 16px;
    display: inline-block;
    vertical-align: middle;
    line-height: initial;
    text-align: left; // 修正文字对齐方式
}
```

##### 2.3 writing-mode
writing-mode可以改变文字的显示方向

所有水平方向上的css属性，都会变为垂直方向上的属性，比如`text-align`,通过`writing-mode`和`text-align`就可以做到水平和垂直方向的居中了

```
<div class="wp">
    <div class="wp-inner">
        <div class="box">hello world</div>
    </div>
</div>

.wp{
    writing-mode: vertical-lr;
    text-align: center;
}
.wp-inner{
    writing-mode: horizontal-tb;
    display: inline-block;
    text-align: center;
    width: 100%;
}
.box{
    display: inline-block;
    margin: auto;
    text-align: left;
}

```

##### 2.4 table
```
<table>
    <tbody>
        <tr>
            <td class="wp">
                <div class="box">hello world</div>
            </td>
        </tr>
    </tbody>
</table>

// table 单元格中的内容天然就是垂直居中的，只需要添加一个水平剧中属性就可以了

.wp{
    text-align: center;
}
.box{
    display: inline-block;
}
```


##### 2.5 css-table

```
<div class="wp">
    <div class="box">hello world</div>
</div>

.wp{
    display: table-cell;
    text-align: center;
    vertical-align: middle;
}
.box{
    display: inline-block;
}
```

##### 2.6 flex

```
<div class="wp">
    <div class="box">hello world</div>
</div>

.wp{
    display: flex;
    justify-content: center;
    align-items: center;
}
```

##### 2.7 grid

```
<div class="wp">
    <div class="box">hello world</div>
</div>

.wp{
    display: grid;
}
.box{
    align-self: center;
    justify-self: center;
}
```

对比各个方式的优缺点，简单总结下：
* PC端有兼容性要求，宽高固定，推荐absolute + 负margin
* PC端游兼容性要求,宽高不固定,推荐css-table
* PC 端无兼容性要求,推荐flex
* 移动端推荐使用flex
