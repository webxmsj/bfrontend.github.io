---
title: 'display 与 visibility 的恩怨情仇'
date: 2018-10-06 20:57:38
tags: [技术]
published: true
hideInList: false
feature: https://imgoss.bfrontend.com/2019-05-31-155054.jpg
---

## 前言
> 还记得面试时被问起"请说说`display:none`和`visibility:hidden`的区别"，可能很多人的答案是：
> `display:none`不占用原来的位置，而`visibility:hidden`占用原来的位置
> 其实不止那么简单呢！本文我们将一起深究它俩的恩怨情仇

## 深入`display:none`
我们都清楚当元素设置`display:none`后界面上将不会显示该元素，并且该元素不占布局空间，但我们仍然可以通过Javascript操作该元素。但为什么会这样呢？
这个涉及到浏览器的渲染原理：浏览器会解析HTML标签生成DOM Tree,解析CSS生成CSSOM,然后将DOM Tree和CSSOM合成生成Render Tree,元素在Render Tree中对应0或多个盒子，然后浏览器以盒子模型的信息布局和渲染界面。而设置为`display:none`的元素则在Render Tree中没有生成对应的盒子模型,因此后续的布局、渲染工作自然没它什么事了,至于DOM操作还是可以的。
但除了上面的知识点外,还有以下8个点我们需要注意的

* 原生默认`display:none`的元素
其实浏览器原生元素中有不少自带`display:none`的元素，如
`link`,`script`,`style`,`dialog`,`input[type=hidden]`等

* HTML5中新增hidden布尔属性,让开发者自定义元素的隐藏性

```
/* 兼容原生不支持hidden属性的浏览器 */
[hidden]{
    display:none;
}
<span hidden>不可见</span>
```

* 父元素为`display:none`，子元素也难逃一劫

```
.hidden{
    display:none;
}
.visible{
    display:block;
}
<div class="hidden">
    this is parent
    <div class="visible">son </div>
</div>
```

* 无法获取焦点

```
<input type="hidden">
<div tabindex="1" style="display:none;">hidden</div>
```

* 无法响应任何事件，无论是捕获、命中目标和冒泡阶段均不可以
由于`display:none`的元素根本不会再界面上渲染，就是连1个像素的都不占，因此自然无法通过鼠标点击命中，而元素也无法获取焦点，那么也不能成为键盘事件的命中目标；而父元素的display为none时，子元素的display必定为none，因此元素也没有机会位于事件捕获或冒泡阶段的路径上，因此`display:none`的元素无法响应事件

* 不耽误form表单提交数据
虽然我们无法看到`display:none`的元素，但当表单提交时依然会将隐藏的input元素的值提交上去。
```
<form>
    <input type="hidden" name="id">
    <input type="text" name="uid" style="display:none">
</form>
```

* css中的counter会忽略`display:none`的元素

```
.start{
    counter-reset: son 0;
}
.son{
    counter-increment: son 1;
}
.son::before{
    content: counter(son);
}
<div class="start">
    <div class="son">son1</div>
    <div class="son" style="display:none">son2</div>
    <div class="son">son3</div>
</div>
```

* Transition 对`display` 的变化不感冒

* display变化时将触发reflow
撇开`display:none`，我们看看`display:block`表示元素位于BFC中，而`display:inline`则表示元素位于IFC中，也就是说`display`的作用就是设置元素所属的布局上下文，若修改`display`值则表示元素采用的布局方式已发生变化，怎么可能不触发reflow

## 深入`visibility`

visibility有两个不同的作用
1. 用于隐藏表格的行和列
2. 用于在不触发布局的情况下隐藏元素

4个有效值
1. visible 
在界面上显示
2. hidden 
让元素在页面上不可见，但保留元素原来占有的位置
3. collapse 
用于表格子元素(如`tr`、`tbody`、`col`,`colgroup`)时效果和`display:none`一样，用于其他元素上时则与`visibility:hidden`一样。不过由于各浏览器实现效果均有出入，因此一般不会使用这个值
4. inherit
继承父元素的`visibility`值

对比清楚`display:none`和`visibility:hidden`

1. 父元素为`visibility:hidden`,而子元素可以设置为`visibility:visible`并且生效

```
div{
    border: solid 2px blue;
}
.visible{
    visibility: visible;
}
.hidden{
    visibility: hidden;
}
<div class="hidden">
    this is parent
    <div class="visible">
        this is son
    </div>
</div>
```

2. 和`display:none`一样无法获取焦点
3. 可在冒泡阶段响应事件
4. 和`display:none`一样不妨碍form表单的提交
5. CSS中的counter不会忽略
6. Transition对`visibility`的变化有效
7. visibility不会触发reflow










