---
title: JS中几种循环的区别与相同点
data: 2018-11-30 16:55:01
tag:
- JS
- 循环
categories: 前端
---

# 循环语句，用于重复执行某个操作，它有多种形式。

> while循环

while语句的循环条件是一个表达式（express），必须放在圆括号中。代码块部分，如果只有一条语句（statement），可以省略大括号，否则就必须加上大括号。

```
while (expression)
  statement;

// 或者

while (expression) statement;
```

下面写个例子

```
var i = 0;

while (i < 100) {
  console.log('i当前为：' + i);
  i += 1;
}
```

上面的代码将循环100次，直到i等于100为止。

下面的例子是一个无限循环，因为条件总是为真。

```
while (true) {
  console.log('Hello, world');
}
```

> for循环

for语句是循环命令的另一种形式，可以指定循环的起点、终点和终止条件。它的格式如下。

```
for (initialize; test; increment)
  statement

// 或者

for (initialize; test; increment) {
  statement
}
```

for语句后面的括号里面，有三个表达式。

> - 初始化表达式（initialize）：确定循环的初始值，只在循环开始时执行一次。
> - 测试表达式（test）：检查循环条件，只要为真就进行后续操作。
> - 递增表达式（increment）：完成后续操作，然后返回上一步，再一次检查循环条件。

eg:

```
var x = 3;
for (var i = 0; i < x; i++) {
  console.log(i);
}
// 0
// 1
// 2
```

上面代码中，初始化表达式是var i = 0，即初始化一个变量i；测试表达式是i < x，即只要i小于x，就会执行循环；递增表达式是i++，即每次循环结束后，i增大1。

所有for循环，都可以改写成while循环。上面的例子改为while循环，代码如下

```
var x = 3;
var i = 0;

while (i < x) {
  console.log(i);
  i++;
}
```

for语句的三个部分（initialize，test，increment），可以省略任何一个，也可以全部省略。

```
for ( ; ; ){
  console.log('Hello World');
}
```

上面代码省略了for语句表达式的三个部分，结果就导致了一个无限循环。

> do…while循环

do…while循环与while循环类似，唯一的区别就是先运行一次循环体，然后判断循环条件。

```
do
  statement
while (expression);

// 或者

do {
  statement
} while (expression);
```

不管条件是否为真，do..while循环至少运行一次，这是这种结构最大的特点。另外，while语句后面的分号不能省略。

```
var x = 3;
var i = 0;

do {
  console.log(i);
  i++;
} while(i < x);
```

> break语句和continue语句
>
> break语句和continue语句都具有跳转作用，可以让代码不按既有的顺序执行。
>
> break语句用于跳出代码块或循环。

```
var i = 0;

while(i < 100) {
  console.log('i当前为：' + i);
  i++;
  if (i === 10) break;
}
```

上面代码只会执行10次循环，一旦i等于10，就会跳出循环。

> for循环也可以使用break语句跳出循环。

```
for (var i = 0; i < 5; i++) {
  console.log(i);
  if (i === 3)
    break;
}
// 0
// 1
// 2
// 3
```

上面代码执行到i等于3，就会跳出循环

> continue语句用于立即终止本轮循环，返回循环结构的头部，开始下一轮循环。

```
var i = 0;

while (i < 100){
  i++;
  if (i%2 === 0) continue;
  console.log('i当前为：' + i);
}
```

上面代码只有在i为奇数时，才会输出i的值。如果i为偶数，则直接进入下一轮循环。

> 如果存在多重循环，不带参数的break语句和continue语句都只针对最内层循环。
>
> 标签（label）

JavaScript语言允许，语句的前面有标签（label），相当于定位符，用于跳转到程序的任意位置，标签的格式如下:

```
label:
  statement
```

> 标签可以是任意的标识符，但是不能是保留字，语句部分可以是任意语句。

标签通常与break语句和continue语句配合使用，跳出特定的循环。

```
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) break top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
```

上面代码为一个双重循环区块，break命令后面加上了top标签（注意，top不用加引号），满足条件时，直接跳出双层循环。如果break语句后面不使用标签，则只能跳出内层循环，进入下一次的外层循环。

> continue语句也可以与标签配合使用。

```
top:
  for (var i = 0; i < 3; i++){
    for (var j = 0; j < 3; j++){
      if (i === 1 && j === 1) continue top;
      console.log('i=' + i + ', j=' + j);
    }
  }
// i=0, j=0
// i=0, j=1
// i=0, j=2
// i=1, j=0
// i=2, j=0
// i=2, j=1
// i=2, j=2
```

上面代码中，continue命令后面有一个标签名，满足条件时，会跳过当前循环，直接进入下一轮外层循环。如果continue语句后面不使用标签，则只能进入下一轮的内层循环。