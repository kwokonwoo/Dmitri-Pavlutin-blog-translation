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
