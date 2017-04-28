---
title: JavaScript基础知识
date: 2017-04-28 20:46:23
tags:
 - JavaScript
---

# 快速入门
JavaScript代码可以直接嵌在网页的任何地方，不过通常我们都把JavaScript代码放到`<head>`中：
```html
<html>
<head>
   <script>
     alert('Hello, world'); // 页面弹出Hello，world提示框
   </script>
</head>
<body>
   ...
</body>
</html>
```

由`<script>...</script>`包含的代码就是JavaScript代码，它将直接被浏览器执行。

第二种方法是把JavaScript代码放到一个单独的.js文件，然后在HTML中通过`<script src="..."></script>`引入这个文件：
```html
<html>
<head>
  <script src="abc.js"></script>
</head>
<body>
  ...
</body>
</html>
```
页面加载时，`abc.js`会被浏览器执行。

把JavaScript代码放入一个单独的.js文件中更利于维护代码，并且多个页面可以各自引用同一份.js文件。

可以在同一个页面中引入多个.js文件，还可以在页面中多次编写`<script> js代码... </script>`，浏览器按照顺序依次执行。

`<script>`标签有一个`type`属性，该属性默认值为`text/javascript`，编写代码为JavaScript时可以缺省。

# 基本语法

## 语法
JavaScript的语法和Java语言类似，每个语句以`;`结束，语句块用`{...}`。但是，JavaScript并不强制要求在每个语句的结尾加`;`，浏览器中负责执行JavaScript代码的引擎会自动在每个语句的结尾补上`;`。

下面的一行代码就是一个完整的赋值语句：
```javascript
var x = 1;
```

下面的一行代码包含两个语句，每个语句用;表示语句结束：
```javascript
var x = 1; var y = 1; // 不建议一行写多个语句
```

语句块是一组语句的集合，例如，下面的代码先做了一个判断，如果判断成立，将执行`{...}`中的所有语句：
```javascript
if (2 > 1) {
    x = 1;
    y = 2;
    z = 3;
}
```
注意花括号`{...}`内的语句具有缩进，通常是4个空格。缩进不是JavaScript语法要求必须的，但缩进有助于我们理解代码的层次，所以编写代码时要遵守缩进规则。很多文本编辑器具有“自动缩进”的功能，可以帮助整理代码。

`{...}`还可以嵌套，形成层级结构：
```javascript
if (2 > 1) {
    x = 1;
    y = 2;
    z = 3;
    if (x < y) {
        z = 4;
    }
    if (x > y) {
        z = 5;
    }
}
```

## 注释

以`//`开头直到行末的字符被视为行注释，注释是给开发人员看到，JavaScript引擎会自动忽略：
```javascript
// 这是一行注释
alert('hello'); // 这也是注释
```

另一种块注释是用`/*...*/`把多行字符包裹起来，把一大“块”视为一个注释：
```javascript
/* 从这里开始是块注释
仍然是注释
仍然是注释
注释结束 */
alert('hello');
```

## 大小写

JavaScript严格区分大小写，如果弄错了大小写，程序将报错或者运行不正常。