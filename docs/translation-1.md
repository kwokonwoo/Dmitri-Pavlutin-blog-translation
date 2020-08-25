# 7个简单却棘手的JavaScript面试问题

[7 Simple but Tricky JavaScript Interview Questions](https://dmitripavlutin.com/simple-but-tricky-javascript-interview-questions/)

在软件开发中，最让我累觉不爱的事情是：

1. 代码面试
2. 坑爹的产品经历和猪队友

不是JavaScript、`this`、CSS、IE，而是上面这两种。

如果你是一位擅长JavaScript的高级工程师，那么很有可能在代码面试中被问到一些棘手的问题。

这不公平，一些不知天高地厚的人试图掂掂你有几斤几两。这可不是什么令人愉悦的体验。

对此你能做什么呢？

要记住：熟能生巧。深入、反复地学习JavaScript会提升你的编程和面试技巧。

在这篇博客中，你将学习7个看起来简单，但实际上棘手的JavaScript面试问题。

尽管问题看起来是随机的，但是这些问题涉及JavaScript的重要概念。因此最好在下一次面试之前练习它们！

### 问题一：随机全局变量
```javascript
function foo() {
  let a = b = 0;
  a++;
  return a;
}

foo();
typeof a; // => ???
typeof b; // => ???
```
我们来看一下第二行代码：`let a = b = 0`。这段语句声明了一个局部变量`a`，与此同时，它也声明了一个全局变量`b`。

因为在函数`foo()`作用域和全局作用域中都没有声明变量`b`，所以JavaScript把`b = 0`表达式当成`window.b = 0`。

换句话说，`b`是偶然间创建的全局变量。

[Accidental global variables](img/accitental-global-variables.webp)

在浏览器中，之前的代码等价于：
```javascript
function foo() {
  let a;
  window.b = 0;
  a = window.b;
  a++;
  return a;
}

foo();
typeof a;        // => 'undefined' 
typeof window.b; // => 'number'
```
`typeof a`是`undefined`，因为变量`a`在函数`foo()`的作用域中，外部作用域无法获取`a`。

因为`b`是值为`0`的全局变量，所以`typeof b`显示为'number'。
