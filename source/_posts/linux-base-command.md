---
title: linux 基础命令
date: 2019-11-19 20:45:59
tags: 
  - linux
categories:
  - linux
feature: 
---

程序员 跑路前执行的 命令 `rm -rf /*`
都9012了， 前端工程师 岂能不会一些基本的linux 命令
<!-- more -->

### grep 搜索文件中指定关键字
grep "string" [选项] file
```shell
// example
grep "React" index.js  // 查找文件中所有`React` 关键字
grep -i "REact" index.js // 不区分大小写的进行搜索
grep -c "react" index.js // 找到给定字符串/模式匹配的行数
```

### cat 命令
一次显示整个文件
```
cat index.js
```
创建一个文件，并将前面命令的输出内容填充进去
```
cat > filename
```
将几个文件合并为一个文件
```
cat a.js b.js > c.js
```

### tail 命令 查看文档的内容
tail [选项] a.js
默认显示文档的最后10行
常用参数
-f 循环读取。
+ 从xx行到结尾
-c 最后xx行

### find 命令搜索文件
```
find path -name filename
find . -name index.js  // 查找所有名为index.js 的文件
find . -name "*.js"  // 查找指定类型的文件
```

### wget someurl  下载文件
### tree 以树状图列出目录的内容

顺序执行多条命令：command1;command2;command3  简单的顺序指令可以通过`;`进行分割
有条件的执行多条命令: which command1 && command2 || command3 // `&&` 前一命令执行成功执行下一条命令 `||` 执行不成功时执行下一个
`$?`: 存储上一次命令的返回结果

```
which git>/dev/null && git --help // 如果存在git命令 执行git --help 命令
echo $?
```

### 管道
```
ls -l /etc | more // 分页显示/etc目录中内容的详细信息
echo "Hello world" | cat > hello.txt // 将一个字符串输入到一个文件中
```
