## JavaScript加性操作符详解

[JavaScript Addition Operator in Details](https://dmitripavlutin.com/javascriptss-addition-operator-demystified/)

JavaScript是一门很棒的语言。我喜欢它是因为它的灵活性，你可以按照你喜欢的方式做事：改变变量类型，动态地给对象添加方法或属性，对不同类型变量使用操作符等等。

但是，这种灵活是有代价的。开发人员要会如何处理不同操作符的类型转换：加（`+`）、相等（`==`和`===`）、不等（`!=`和`!==`）等。许多操作符有自己的方式来处理类型转换。

#### 加性操作符
最常用的运算符之一是加性运算符：`+`，加性运算符被用来拼接字符串或数字求和：
1. 字符串拼接：
```javascript
var result = "Hello, " + "World!";
// string + string = string (concatenation)
// "Hello, World!"
```
2. 数字算术求和：
```javascript
var result = 10 + 5;
// number + number = number (addition)
// 15
```

JavaScript允许使用对象、数组、`null`、`undefined`作为操作数。让我们搞清楚转换的一般规则吧。

#### 转换规则
以下面的等式为例来看一下JavaScript在加性操作中是如何转换类型的：

`操作数 + 操作数 = 结果` 
1. 如果至少有一个操作数是对象，则将其转换为原始值（字符串、数字、布尔类型）；
2. 转换过后，如果至少有一个操作数是字符串类型，则将第二个操作数转换成字符串类型并执行拼接操作；
3. 其他情况下，两个操作数都被转换为数字类型并执行算术求和。

如果两个操作数都是原始类型，操作符会检查是否存在字符串类型值，如果存在，执行拼接操作。其他情况则一律转换为数字并求和。

#### 对象转原始值
