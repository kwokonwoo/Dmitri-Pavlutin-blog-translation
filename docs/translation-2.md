## JavaScript中“this”详解

[Gentle Explanation of "this" in JavaScript](https://dmitripavlutin.com/gentle-explanation-of-this-in-javascript/)

### 1. this的奥秘
`this`关键词曾经困扰过我相当长的一段时间，`this`是一个强大的功能，但与此同时也需要付出努力才能够理解。

从Java、PHP和其他标准语言来看，`this`被视作当前对象在类方法中的实例。大多数情况下，`this`不可以在方法外使用，因此也不会造成什么困扰。

在JavaScript中情况就大不一样了：`this`是函数的当前执行上下文。JavaScript有4种函数调用类型：
- 函数调用：`alert('Hello World!')`
- 方法调用：`console.log('Hello World!')`
- 构造函数调用：`new RegExp('\\d')`
- 间接调用：`alert.call(undefined, 'Hello World!')`

每种调用类型都以自己的方式定义上下文，因此`this`会与开发者的预期有所差距。

<img src="../img/Gentle-explanation-of-this--7--1.png" width = "536" height = "610" alt="The mystery of this" />

此外，[严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)也会影响执行上下文。

理解`this`的关键是对函数调用以及它如何影响上下文有一个清晰的认知。本文着重于对调用的解释，函数调用如何影响`this`以及论述识别上下文时会遇到的坑。

在开始之前，我们先来熟悉一些术语：
- 函数的调用就是执行构成函数主题的代码，或者只是简单的调用函数。例如`parseInt`函数调用为`parseInt('15')。
- 调用的上下文是函数体内`this`的值。例如在`map.set('key', 'value')`调用中上下文为`map`。
- 函数作用域是指函数体内可访问的变量、对象、函数的集合。

### 2. 函数调用
当一个表达式是函数对象，后接开括号`(`、逗号分隔的参数表达式列表和右括号`)`时，执行函数调用。例如`parseInt('18')`。

函数调用表达式不能是创建方法调用的[属性访问器](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors)`obj.myFunc()`。例如`[1,5].join(',')`**不是**一个函数调用而是一个方法调用。请记住它们之间的区别。

函数调用的简单示例：
```javascript
function hello(name) {
  return 'Hello ' + name + '!';
}
// 函数调用
const message = hello('World');
console.log(message); // 'Hello World!'
```

`hello('World')`是函数调用：`hello`表达式是一个函数对象，后接一对带有'World'参数的括号。

更高级一点的例子是[立即调用函数表达式](https://zh.wikipedia.org/wiki/%E7%AB%8B%E5%8D%B3%E8%B0%83%E7%94%A8%E5%87%BD%E6%95%B0%E8%A1%A8%E8%BE%BE%E5%BC%8F)（immediately invoked function expression）：
```javascript
const message = (function(name) {
  return 'Hello ' + name + '!';
})('World');
console.log(message); // 'Hello World!'
```
立即调用函数表达式也是函数调用：第一对括号`(function(name) {...})`是值为函数对象的表达式，后接参数为`World`的一对括号。

#### 2.1 函数调用中的'this'
