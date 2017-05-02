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

---
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

---
# 数据类型和变量

## 数据类型

### Number

JavaScript不区分整数和浮点数，统一用Number表示，以下都是合法的Number类型：
```javascript
123; // 整数123
0.123; // 浮点数0.123
1.23e3; // 科学计数法表示1.23 x 1000 等于 1230
-123; // 负数-123
NaN; // NaN 表示 not a number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

Number可以直接做四则运算：
```javascript
1 + 2; // 3
(1 + 2)  * 5 / 2; // 7.5
2 / 0; // Infinity
0 / 0; // NaN
10 % 3; // 1
10.5 % 3; // 1.5
```

### 字符串

字符串是以单引号`'`或双引号`"`括起来的任意文本，比如`'abc'`，`"xyz"`等等。`''`或`""`本身只是一种表示方式，不是字符串的一部分，因此，字符串`'abc'`只有`a`，`b`，`c`这3个字符。

如果`'`本身也是一个字符，那就可以用`""`括起来，比如`"It's OK"`包含的字符是`I`，`t`, `'`，`s`，`空格`，`O`，`K`这7个字符。

如果字符串内部既包含`'`又包含`"`，可以用转义字符`\`来标识，比如：
```javascript
'It\'s \"OK\"'; // It's "OK"
```

转义字符`\`可以转义很多字符，比如`\n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\`。

ASCII字符以`\x##`形式的十六进制表示，例如：
```javascript
'\x41'; // 表示'A'
'\x61'; // 表示'a'
```

Unicode字符以`\u####`形式表示，例如：
```javascript
'\u4e2d\u6587'; // 表示'中文'
```

### 布尔值

布尔值和布尔代数的表示完全一致，一个布尔值只有`true`、`false`两种值，可以直接用true、false表示布尔值，也可以通过布尔运算计算出来：
```javascript
true; // true
false; // false
1 < 2; // true
2 >= 3; // false
```

`&&`运算是与运算，只有所有都为`true`，`&&`运算结果才是`true`：
```javascript
true && true; // true
true && false; // false
false && false; // false
false && true && false; // false
```

`||`运算是或运算，只要其中有一个为`true`，`||`运算结果就是`true`：
```javascript
true || true; // true
true || false; // true
false || false; // false
false || true || false; // true
```

`!`运算是非运算，它是一个单目运算符，把`true`变成`false`，`false`变成`true`：
```javascript
!true; // false
!false; // true
!(2 > 3); // true
```

对Number做比较时，可以通过比较运算符得到一个布尔值：
```javascript
2 > 3; // false
3 >= 2; // true
1 == 1; // true;
```

JavaScript允许对任意数据类型做比较：
```javascript
false == 0; // true
false === 0; // false
```

JavaScript在设计时，有两种比较运算符：
* `==`比较，自动转换数据类型再比较，很多时候，会得到非常诡异的结果；
* `===`比较，不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比较。
由于JavaScript这个设计缺陷，***不要***使用`==`比较，始终坚持使用`===`比较。

`NaN`这个特殊的Number与所有其他值都不相等，包括它自己：
```javascript
NaN === NaN; // false
```

### null和undefined

`null`表示一个“空”的值，它和`0`以及空字符串`''`不同，`0`是一个数值，`''`表示长度为0的字符串，而`null`表示“空”。
`undefined`表示"未定义"。
JavaScript的设计者希望用`null`表示一个空的值，而`undefined`表示值未定义。事实证明，这并没有什么卵用，区分两者的意义不大。大多数情况下，我们都应该用`null`。`undefined`仅仅在判断函数参数是否传递的情况下有用。

### 数组

数组是一组按顺序排列的集合，集合的每个值称为元素。JavaScript的数组可以包括任意数据类型。例如：
```javascript
[1, 2, 3.45, 'Hello', null, true]; // 创建数组[1, 2, 3.45, 'Hello', null, true]
```
上述数组包含6个元素。数组用`[]`表示，元素之间用`,`分隔。

另一种创建数组的方法是通过`arr()`函数实现：
```javascript
new arr(1, 2, 3.45, 'Hello', null, true); // 创建数组[1, 2, 3.45, 'Hello', null, true]
```

数组的元素可以通过索引来访问，索引的起始值为0：
```javascript
var arr = [1, 2, 3.45, 'Hello', null, true];
arr[0]; // 1
arr[2]; // 3.45
arr[3]; // Hello
arr[5]; // true
arr[6]; // 索引超出了范围，返回undefined
```

#### `length`属性
数组的`length`属性返回数组的长度：
```javascript
var arr = [1, 2, 3.45, 'Hello', null, true];
arr.length; // 6
```

给数组的`length`属性赋值会改变数组的长度：
```javascript
var arr = [1, 2, 3.45, 'Hello', null, true];
arr.length; // 6
arr.length = 8; // [1, 2, 3.45, 'Hello', null, true, undefined, undefined]
arr.length = 4; // [1, 2, 3.45, 'Hello']
```

数组可以通过索引值修改元素的值：
```javascript
var arr = [1, 2, 3.45, 'Hello', null, true];
arr[0] = 'A'; // 数组变为['A', 2, 3.45, 'Hello', null, true]
```

如果索引值超过了原数组长度，修改数组元素会改变数组的长度：
```javascript
var arr = [1, 2, 3.45, 'Hello', null, true];
arr[6] = 'A'; // 数组变为[1, 2, 3.45, 'Hello', null, true, 'A']
```

#### `indexOf()`
数组可以通过`indexOf()`来搜索一个指定的元素的位置：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
arr.indexOf(1); // 1的索引位置为0
arr.indexOf('2'); // '2'的索引位置为1
arr.indexOf(2); // 2不是数组元素，索引为-1
arr.indexOf('Hello'); // 'Hello'的索引位置为3
arr.indexOf(true); // true的索引位置为5
```

#### `slice()`
`slice()`类似String的`substring()`，截取数组的部分元素，返回一个新的数组：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
arr.slice(0,3); // 从索引0开始，到索引3，不包括索引3：[1, '2', 3.45]
arr.slice(3); // 从索引3开始到结束：['Hello', null, true]
```
`slice()`的起止参数包括开始索引，不包括结束索引。

如果不给`slice()`传任何参数，表示截取所有元素，可以利用这一点来复制并创建一个新的数组：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
var arrCopy = arr.slice(); // [1, '2', 3.45, 'Hello', null, true]
arr === arrCopy; // false
```

#### `push()和pop()`
`push()`向数组的末尾添加若干新元素，`pop()`则将数组的末尾最后一个元素删掉：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
arr.push('6'); // [1, '2', 3.45, 'Hello', null, true, '6'];
arr.push(7,'A'); // [1, '2', 3.45, 'Hello', null, true, '6', 7,'A'];
arr.pop(); // pop返回 'A'，原数组变为[1, '2', 3.45, 'Hello', null, true, '6', 7];
arr.pop(); arr.pop(); arr.pop();arr.pop(); arr.pop(); arr.pop();arr.pop(); arr.pop(); // 连续pop8次，原数组变为[]
arr.pop(); // 空数组pop不会报错，返回undefined，原数组变为[]
```

#### `unshift()和shift()`
`unshift()`向数组的头部添加若干新元素，`shift()`则将数组的头部第一个元素删掉：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
arr.unshift('6'); // ['6'，1, '2', 3.45, 'Hello', null, true];
arr.unshift(7,'A'); // [7, 'A'，'6'，1, '2', 3.45, 'Hello', null, true];
arr.shift(); // pop返回 7，原数组变为['A'，'6'，1, '2', 3.45, 'Hello', null, true];
arr.shift(); arr.shift(); arr.shift();arr.shift(); arr.shift(); arr.shift();arr.shift(); arr.shift(); // 连续shift8次，原数组变为[]
arr.shift(); // 空数组shift不会报错，返回undefined，原数组变为[]
```

#### `sort()`
`sort()`对数组进行排序，默认按照自然顺序排序：
```javascript
var arr = [6, 4, 1, 3];
arr.sort(); // [1, 3, 4, 6]
```

#### `splice()`
`splice()`从指定的索引开始删除若干元素，然后再从该位置添加若干元素：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
arr.splice(2, 3, 'A', 'B'); // 从索引2开始删除3个元素，然后添加两个元素，返回：[3.45, 'Hello', null]，原数组变为：[1, '2', A，B, true]
arr.splice(2, 2); // 从索引2开始删除两个元素，不添加任何元素，返回：[A, B]，原数组变为：[1, '2', true]
arr.splice(2, 0, 'A', 'B'); // 从索引2开始不删除元素，然后添加两个元素，返回 []，元素组变为：[1, '2', A，B, true]
```

#### `reverse()`
`reverse()`对数组进行元素反转：
```javascript
var arr = [4, 3, 2, 1];
arr.reverse(); // [1, 2, 3, 4]
```

#### `concat()`
`concat()`将当前数组与另一个数组或若干元素拼接起来，返回新的数组，但是并不修改当前数组：
```javascript
var arr = [1, '2', 3.45, 'Hello', null, true];
arr.concat([1, 2, 3]); // [1, '2', 3.45, 'Hello', null, true, 1, 2, 3]
arr.concat(4, 5, ['A', 'B']) // [1, '2', 3.45, 'Hello', null, true, 1, 2, 3，4，5，'A'，'B']
```

#### `join()`
`join()`将数组的元素用指定的字符拼接起来：
```javascript
var arr = [1, '2', 3.45];
arr.join('-'); // '1-2-3.45'
arr = [1, '2', 3.45, 'Hello', null, undefined, NaN, Infinity, true];
arr.join('-'); // 当数组中包含：null, undefined, NaN, Infinity等元素时，null与undefined拼接时留空：'1-2-3.45-Hello---NaN-Infinity-true'
```

### 对象

JavaScript的对象是一组由键-值组成的无序集合，例如：
```javascript
var student  = {
    name : 'Jimmy',
    age : 20,
    sex : '男'
};
```
JavaScript对象的键都是字符串类型，值可以是任意数据类型。上述`student`对象一共定义了3个键值对，其中每个键又称为对象的属性，例如，`student`的`name`属性为`Jimmy`，`sex`属性为`男`，要获取一个对象的属性，我们用对象*`变量.属性名`*的方式：：
```javascript
student.name; // Jimmy
student.sex; // 男
```

## 变量

变量在JavaScript中用变量名表示，变量名是大小写英文、数字、$和_的组合，且不能以数字开头。变量名不能是JavaScript的关键字，如`if`、`else`、`while`等。声明变量使用`var`关键字，比如：
```javascript
var a; // 声明了一个变量a，值为undefined
var $b = 1; // 声明了一个变量$b，同时给$b赋值，值为1
var c = true; // 声明了一个布尔型变量c，值为true
```

JavaScript为弱类型语言，使用等号`=`对变量进行赋值。可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，但是要注意只能用`var`声明一次，例如：
```javascript
var a = 1; // 声明了一个Number型变量a，并且赋值为1
a = 'ABC'; // a重新赋值为ABC，此时a变成字符型 
```

### strict模式

JavaScript在设计之初，为了方便初学者学习，并不强制要求用`var`声明变量。这个设计错误带来了严重的后果：如果一个变量没有通过`var`声明就被使用，那么该变量就自动被声明为全局变量：
```javascript
a = 1; // a是全局变量
```

在同一个页面的不同的JavaScript文件中，如果都不用`var`声明，恰好都使用了变量`a`，将造成变量`a`互相影响，产生难以调试的错误结果。

使用`var`声明的变量则不是全局变量，它的范围被限制在该变量被声明的函数体内，同名变量在不同的函数体内互不冲突。

为了修补JavaScript这一严重设计缺陷，ECMA在后续规范中推出了`strict`模式，在strict模式下运行的JavaScript代码，强制通过`var`声明变量，未使用`var`声明变量就使用的，将导致运行错误。

启用strict模式的方法是在JavaScript代码的第一行写上：
```javascript
`use strict`;
```