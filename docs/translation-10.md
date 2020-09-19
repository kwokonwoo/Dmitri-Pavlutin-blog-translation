### JavaScript之继承——理解构造器属性

JavaScript有一个有趣的继承机制：[原型](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)。和我一样，大多数入门JavaScript的开发者一开始都很能理解原型。

JavaScript中的所有类型（`null`和`undefined`除外）都有一个构造器属性，该属性是继承的一部分。例如：
```javascript
var num = 150;
num.constructor === Number // => true
var obj = {};
obj.constructor === Object // => true
var reg = /\d/g
reg.constructor === RegExp; // => true
```

这篇文章中将深入探讨对象的`constructor`属性，作为该类的公共标识，意味着它可以用来：
- 标识对象属于哪个类（代替`instanceof`）
- 从对象或原型中引用构造函数
- 获取类名

#### 1. 基本类型的构造器
JavaScript中的基本类型是数字、布尔值、字符串、`symbol`、（ES6）、`null`和`undefined`。除了`null`和`undefined`之外的所有值都有`constructor`属性，该属性指向对象的类型函数：
- 数字类型`Number`：`(1).constructor === Number`
- 布尔类型`Boolean()`: `(true).constructor === Boolean`
- 字符串类型`String()`： `('hello').constructor === String`
- symbols类型`Symbol`： `Symbol().constructor === Symbol`

通过将基本类型的构造器属性和对应的函数比较，能够确定其类型。例如，验证值是否为数字：
```javascript
if (myVariable.construcotr === Number) {
  //当myVariable为数字时执行代码
  myVariable += 1;
}
```

注意，通常不推荐以上这种做法，而建议采用`typeof`（见1.1）。但是在`switch`语句中，这种方法是很有用的，因为它可以减少`if/else`的数量。
```javascript
// myVariable = ...
var type;
switch (myVarialbe.constructor) {
  case Number:
    type = 'number';
    break;
  case Boolean:
    type = 'boolean';
    break;
  case String:
    type = 'string';
    break;
  case Symbol:
    type = 'symbol';
    break;
  default:
    type = 'unknown';
    break;
}
```

##### 1.1 基本类型值的对象包装
使用`new`运算符调用函数时，会创建基本类型的对象包装。包装可以通过`new String('str')`，`new Number(15)`和`new Boolean(false)`创建。但是`Symbol`类型无法创建包装，因为`new Symbol('symbol')`会抛出`TypeError`。
对象包装可以让开发者为一个基本类型添加自定义属性和方法，因为JavaScript不允许基本类型有自己的属性。

这些对象的存在可能会给基于构造器确定变量类型的方法造成混乱，因为对象包装与基本类型具有相同的构造器：
```javascript
var booleanObject = new Boolean(false);
booleanObject.constructor === Boolean // => true

var booleanPrimitive = false;
booleanPrimitive.constructor === Boolean // => true
```

#### 2. 原型对象中的构造器
原型中的构造器属性会自动设置为指向构造函数。

```javascript
function Cat(name) {
  this.name = name;
}
Cat.prototype.getName = function() {
  return this.name;
}
Cat.prototype.clone = function() {
  return new this.constructor(this.name);
}
Cat.prototype.constructor === Cat; // => true

```






















