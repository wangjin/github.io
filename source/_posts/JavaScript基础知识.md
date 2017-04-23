---
title: JavaScript基础知识
date: 2017-04-18 20:46:23
tags:
 - JavaScript
---

# 一、如何插入JS
JS代码通常写在<code>`<script></script>`</code>标签中
![](/images/JS标签.jpg)
或者在src中指定外部JS文件路径<code>`<script src="script.js"></script>`</code>来引用外部JS文件
![](/images/外部JS文件.jpg)

# 二、JS在页面中的位置
* 放在`<head>`部分
在页面中head部分放置`<script>`元素，浏览器解析head部分就会执行这个代码，然后才解析页面的其余部分
* 放在`<body>`部分
在页面中body部分放置`<script>`元素，浏览器解析到该语句市就会执行这个代码
![](/images/JS位置.jpg)

# 三、变量
## 1. 定义变量使用关键字var
```
var 变量名
```

## 2. 变量要先声明再赋值
```
var mychar;
mychar = 'javascript';
var mynum = 6;
```

## 3. 变量可以重复赋值
```
var mychar;
mychar='javascript';
mychar='hello';
```

* 在JS中区分大小写，如变量mychar与myChar是不一样的，表示是两个变量
* 变量虽然也可以不声明，直接使用，但不规范，需要先声明后使用

# 四、函数

## 1. 定义函数
```
function 函数名()
{
     函数代码;
}
```

* function定义函数的关键字
* "函数名"你为函数取的名字
* "函数代码"替换为完成特定功能的代码

# 五、输出内容（document.write）
`document.write()`可用于直接向`HTML`输出流写内容

## 1. 输出内容用""括起，直接输出""号内的内容。
``` 
<script type="text/javascript">
  document.write("I love JavaScript！"); //内容用""括起来，""里的内容直接输出。
</script>
```
## 2. 通过变量，输出内容
```
<script type="text/javascript">
  var mystr="hello world!";
  document.write(mystr);  //直接写变量名，输出变量存储的内容。
</script>
```

## 3. 输出多项内容，内容之间用+号连接。
```
<script type="text/javascript">
  var mystr="hello";
  document.write(mystr+"I love JavaScript"); //多项内容之间用+号连接
</script>
```

## 4. 输出HTML标签，并起作用，标签使用""括起来。
```
<script type="text/javascript">
  var mystr="hello";
  document.write(mystr+"<br>");//输出hello后，输出一个换行符
  document.write("JavaScript");
</script>
```