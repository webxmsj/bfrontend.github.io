---
title: 'flutter系列文章-001.flutter 初探'
date: 2019-07-04 20:57:38
tags:
  - flutter
  - dart
categories:
  - flutter
---
## flutter 初探

### 1. 环境安装

- 下载flutter sdk 配置环境变量
- jetbrains相关编辑器安装flutter dart插件
- flutter doctor 执行校验

### 2. 框架总览

1. 运行启动入口

```dart
void main(
	runApp(
		new Center(
			child: new Text('home')
		)
	)
)
```

2. 组件

> Widget 类似react 也分为有状态组件(StatefulWidget)、无状态组件(StatelessWidget)

> [widget文档](https://docs.flutter.io/flutter/widgets/Stack-class.html)

   ```dart
   class home extends StatelessWidget {
   // 主要模块就是复写build 方法，类似react 中render
     @override
     Widget build(BuildContext context) {
   		return new Scaffold(
         appBar: new AppBar(
           title: new Text('首页')
         ),
         body: new Container(
           child: new Text('首页内容')
         )
       );
     }
   }
   ```

   

3. flutter 中常见的一些基础组件

- Text 创建一个带格式的文本

```dart
new Text(
	'内容元素',
	textAlign: TextAlign.center,
	overflow: TextOverflow.ellipsis,
	style: Theme.of(context).primaryTextTheme.title
)
```

- Row、Column 灵活实现行列上的布局

> Row

```dart
new Row(
	children: <Widget>[
		new IconButton(
			icon: new Icon(Icons.menu),
			tooltip: 'Navigation menu',
			onPressed: null // 禁用button
		),
		// 填充剩余可用空间
		new Expanded(
			child: '标题'
		),
		new IconButton(
			icon: new Icon(Icons.search),
			tooltip: 'search',
			onPressed: null
		)
	]
)
```

> Column

```
new Column(
	children: <Widget>[
		new Text('一'),
		new Text('二'),
		new Text('三')
	]
)
```



- Stack 取代线性布局(类似android中的LinearLayout)，实现可以堆叠的Widget ，类似css 中absolute

```dart
new Stack(
	children: <Widget>[
    new Container(
    	width: 100,
      height: 100,
      color: Colors.red
    ),
    new Container(
        padding: EdgeInsets.all(5.0),
        alignment: Alignment.bottomCenter,
        decoration: BoxDecoration(
          gradient: LinearGradient(
            begin: Alignment.topCenter,
            end: Alignment.bottomCenter,
            colors: <Color>[
              Colors.black.withAlpha(0),
              Colors.black12,
              Colors.black45
            ],
          ),
        ),
        child: Text(
          "Foreground Text",
          style: TextStyle(color: Colors.white, fontSize: 20.0),
        )
      )
  ]
)
```



- Container 矩形视觉元素 类似html中div

```dart
new Container(
	height: 60.0, // 逻辑像素，类似浏览器中px
	padding: const EdgeInsets.symmetric(horizontal: 8.0),
	decoration: new BoxDecoration(color: Colors.blue[500])
)
```
