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

<img style="-webkit-user-select: none;margin: auto;cursor: zoom-in;" src="https://dmitripavlutin.com/static/1b389a46f5ed3a0a60724be89e938696/611e8/Gentle-explanation-of-this--7--1.png" width="455" height="302">

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
> 在函数调用中`this`是全局对象。

全局对象取决于执行环境。在浏览器中，全局对象是`window`对象。

<img style="-webkit-user-select: none;margin: auto;cursor: zoom-in;" src="https://dmitripavlutin.com/static/7035b35b8d8dce31376d9839065e58f7/19891/2-1.png" width="455" height="334">

在函数调用中，执行环境是全局对象。

我们来看一下下面函数的环境：
```javascript
function sum(a, b) {
  console.log(this === window); // true
  this.myNumber = 20; // 给全局对象添加'myNumber'属性
  return a + b;
}
// sum()作为函数被调用
// sum()中的this是全局对象（window）
sum(15, 16); // 31
window.myNumber; // 20
```
在`sum(15, 16)`被调用的时候，JavaScript自动地把`this`设置为全局对象（在浏览器中为`window`）。

当`this`在函数作用域外（最顶层作用域：全局执行环境）时，它也是全局对象：
```javascript
console.log(this === window); // => true
this.myString = 'Hello World!';
console.log(window.myString); // => 'Hello World!'
```
```javascript
<!-- In an html file -->
<script type="text/javascript">
 console.log(this === window); // => true
</script>
```

#### 2.2 严格模式函数调用中的`this`
> 在严格模式下，函数调用中的`this`是`undefined`。

从ECMAScript5.1开始，就可以使用严格模式了，严格模式是具有限制性的JavaScript变体。它提供了更好的安全性和更强大的错误检查。

要启用严格模式，需要将'use strict'指令放在函数体的顶部。

启用后，严格模式会影响执行环境，从而使得在常规函数调用中`this`的值为`undefined`。与2.1的例子相反，执行环境不再是全局对象。

<img style="-webkit-user-select: none;margin: auto;cursor: zoom-in;" src="https://dmitripavlutin.com/static/2a757bd246d0f4e60395273d0aeeb767/42ccd/3-1.png" width="455" height="387">

在严格模式下的函数调用：
```javascript
function multiply(a, b) {
  'use strict'; // enable the strict mode
  console.log(this === undefined); // => true
  return a * b;
}
// multiply() function invocation with strict mode enabled
// this in multiply() is undefined
multiply(2, 5); // => 10
```
在严格模式下调用函数`multiply(2, 5)`时，`this`是`undefined`。

严格模式不仅在当前作用域起作用，在内部作用域（内部声明的函数）也同样起作用：
```javascript
function execute() {
  'use strict'
  
  function concat(str1, str2) {
    //同样采用了严格模式
    console.log(this === undefined); // => true
    return str1 + str2;
  }
  
    // concat()作为函数在严格模式中被调用
    // concat()中this是undefined
    concat('Hello', ' World!'); // => "Hello World!"
}

execute();
```
`use strict`在`execute`的顶部，从而在其作用域启用了严格模式。因为`concat`在`excute`作用域内声明，因此它继承了严格模式。调用`concat('Hello', ' World!')让`this`成为`undefined`。

单个JavaScript文件可能同时包含严格模式和非严格模式。因此，对于相同的调用类型，可能在单个脚本中有不同的行为。
```javascript
function nonStrictSum(a, b) {
  // 非严格模式
  console.log(this === window); // => true
  return a + b;
}

function strictSum(a, b) {
  'use strict';
  // 严格模式启用
  console.log(this === undefined); // => true
  return a + b;
}

// 在非严格模式下调用nonStrictSum()，nonStrictSum()中this是window对象
nonStrict(5, 6); // => 11
// 在严格模式下调用strictSum()，strictSum()中的this是undefined
strictSum(8, 12); // => 20
```

#### 2.3 陷阱：内部函数的`this`
❗函数调用的一个常见误区就是认为内部函数和外部函数的`this`是一样的。

👍实际上内部函数的的环境只取决于它的调用类型，而不是外部函数的环境。

要改变`this`的值，可以使用间接调用来改变内部函数的环境（使用`.call()`或`apply()`，见[5](#jump5)）或者创建绑定函数（使用`.bind()`，见[6](#jump6)）。

下面的例子计算了两个数字的和：
```javascript
const numbers = {
  numbersA: 5,
  numbersB:10,
  
  sum: function() {
    console.log(this === numbers); // => true
    
    function calculate() {
      // this是window（严格模式下为window）
      console.log(this === numbers); // => false
      return this.numberA + this.numberB;
    }
    
    return calculate();
  }
};

numbers.sum(); // => NaN or throws TypeError in strict mode
```
❗`numbers.sum()` 是作用于对象的方法调用（见[3](#jump3)），所以`sum`的环境是`numbers`对象。`calculate()`函数在`sum()`中定义，因此你可能认为`calculate()`中`this`也是`numbers`对象。

`calculate()`是函数调用（而不是方法调用），因此`this`在这里是全局对象`window`（见2.1），在严格模式下是`undefined`。即使外层函数`sum()`的环境`numbers`对象，这里也没有影响。

`numbers.sum()`的调用结果是`NaN`（在严格模式下抛出`TypeError: Cannot read property 'numberA' of undefined`错误）。显然不是预期的结果`5 + 10 = 15`，都是因为`calculate`没有正确的调用。

👍为了解决这个问题，`calculate()`函数必须要和`sum()`方法同一环境下执行，才能访问`numberA`和`numberB`属性。

一个办法就是通过`calculate.call(this)`（函数的间接调用，见第5节）手动的改变`calculate()`的作用域：
```javascript
const numbers = {
  numberA: 5,
  numberB: 10,
  sum: function() {
    console.log(this === number); // => true
    
    function calculate() {
      console.log(this === number); // => true
      return this.numberA + this.numberB;
    }
    
    // 通过.call()方法改变作用域
    return calculate.call(this);
  }
};
numbers.sum(); // => 15
```
`calculate.call(this)`照常执行函数，但是把第一个参数作为作用域。

现在`this.numberA + this.numberB`等价于`numbers.numberA + numbers.numberB`，函数返回预期结果`5 + 10 = 15`。

另一个更好的办法是使用箭头函数：
```javascript
const numbers = {
  numberA: 5,
  numberB: 10,
  sum: function() {
    console.log(this === numbers); // => true
    
    const calculate = () => {
      console.log(this === number); // => true
      return this.numberA + this.numberB;
    }
    
    return calculate();
  }
};

numbers.sum(); // => 15
```
箭头函数词法上绑定`this`，或者更为简单的使用`sum()`方法的`this`。

### 3. 方法调用
**方法**是对象属性中的函数，例如：
```javascript
const myObject = {
  // helloFunction 是方法
  helloFunction: function() {
    return 'Hello World!';
  }
};

const message = myObject.helloFunction();
```
`helloFunction`是`myObject`的方法，使用属性访问器`myObject.helloFunction`可以访问该方法。

当形式为属性访问器的表达式是一个函数对象后加上一个开括号和逗号分隔的参数表达式列表，以及一个闭括号时，执行**方法调用**。

回想之前的例子，`myObject.helloFunction()`是`helloFunction`在对象`myObject`上的方法调用。

还有一些方法调用的例子诸如：`[1, 2].join(',')`或`/\s/.test('beautiful world')`。

区分**函数调用**和**方法调用**十分重要。最主要的区别方法调用需要属性访问器的形式调用函数（`obj.myFunc()`或`obj['myFunc']()`），而函数调用不需要（`myFunc()`）。
```javascript
const words = ['Hello', 'World'];

words.join(', '); // 方法调用
const obj = {
  myMethod() {
    return new Date().toString();
  }
};
obj.myMethod(); // 方法调用

const func = obj.myMethod;
func();              // 函数调用
parseFloat('16.60'); // 函数调用
isNaN(0);            // 函数调用
```
了解函数调用与方法调用之间的区别有助于识别上下文。

#### 3.1 方法调用中的this
> 在方法调用中，`this`是拥有方法的对象。

在对象上调用方法时，`this`是拥有该方法的对象。

<img style="-webkit-user-select: none;margin: auto;cursor: zoom-in;" src="https://dmitripavlutin.com/static/baf7c3a9ba2fa5c631ea32b940d95f95/1c68e/4-1.png" width="455" height="365">

我们来创建一个对象，这个对象用来增加数字的方法：
```javascrip
const calc = {
  num: 0,
  increment() {
    this.num += 1;
    return this.num;
  }
};

// 方法调用：this是calc
calc.increment(); // => 1
calc.increment(); // => 2
```
调用`calc.increment()`使得`increment`函数的上下文是`calc`对象。因此可以用`this.num`增加`number`属性。




### 5. <span id="jump5">简介调用</span>

### 6. <span id="jump6">绑定函数</span>
