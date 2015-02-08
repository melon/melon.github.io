title: javascript中Object初始化的一些概念厘清
date: 2015-02-07 10:08:41
categories:
- IT
- Notes
tags:
- javascript
- Object
---
javascript中Object的初始化相关概念可能或多或少都有些了解，但是有时候也会有些混淆，我觉得有必要厘清一下

Object的初始化方法有三种:
- new Object()
- Object.create()
- literal notation

用法示例：
{% code lang:js %}
var o = {};
var o = { a: "foo", b: 42, c: {} };

var a = "foo", b = 42, c = {};
var o = { a: a, b: b, c: c };

// getter/setter (ES5)
var o = {
  property: function ([parameters]) {},
  get property() {},
  set property(value) {},
};

// Shorthand property names (ES6)
var a = "foo", b = 42, c = {};
var o = { a, b, c };

// Shorthand method names (ES6)
var o = {
  property([parameters]) {},
  get property() {},
  set property(value) {},
  * generator() {}
};

// Computed property names (ES6)
var prop = "foo";
var o = {
  [prop]: "hey",
  ["b" + "ar"]: "there",
};
{% endcode %}

<!--more-->

### 重复的属性名

{% code lang:js %}
var a = {x: 1, x: 2};
console.log(a); // { x: 2}
{% endcode %}

上述代码在**es5严格模式**下被认为是SyntaxError，但在es6中由于computed property names的引入，这个错误被去除了。

### __proto__的修改

1. 采用**__proto__: value**或**"__proto__": value**的形式定义属性，并不会创建一个叫__proto__的属性。如果value是一个**对象**或**null**，这种属性定义会去更改这个对象的内部属性**[[Prototype]]**。（如果不是对象或null，则不会有任何效果）

示例：
{% code lang:js %}
var obj1 = {};
assert(Object.getPrototypeOf(obj1) === Object.prototype);

var obj2 = { __proto__: null };
assert(Object.getPrototypeOf(obj2) === null);

var protoObj = {};
var obj3 = { "__proto__": protoObj };
assert(Object.getPrototypeOf(obj3) === protoObj);

var obj4 = { __proto__: "not an object or null" };
assert(Object.getPrototypeOf(obj4) === Object.prototype);
assert(!obj4.hasOwnProperty("__proto__"));
{% endcode %}

2. 不采用上述的方式修改__proto__的则不被认定为对Prototype的修改，而是当做一般属性的操作来对待

示例：
{% code lang:js %}
var __proto__ = "variable";

var obj1 = { __proto__ };
assert(Object.getPrototypeOf(obj1) === Object.prototype);
assert(obj1.hasOwnProperty("__proto__"));
assert(obj1.__proto__ === "variable");

var obj2 = { __proto__() { return "hello"; } };
assert(obj2.__proto__() === "hello");

var obj3 = { ["__prot" + "o__"]: 17 };
assert(obj3.__proto__ === 17);
{% endcode %}

### Oject.create()

{% code lang:js %}
Object.create(proto[, propertiesObject])
{% endcode %}

Object.create()函数可以用来创建一个带有**指定的prototype对象**和**properties**的新对象。

用Object.create()实现classical inheritance(single inheritance):

{% code lang:js %}
// Shape - superclass
function Shape() {
  this.x = 0;
  this.y = 0;
}

// superclass method
Shape.prototype.move = function(x, y) {
  this.x += x;
  this.y += y;
  console.info('Shape moved.');
};

// Rectangle - subclass
function Rectangle() {
  Shape.call(this); // call super constructor.
}

// subclass extends superclass
Rectangle.prototype = Object.create(Shape.prototype);
Rectangle.prototype.constructor = Rectangle;

var rect = new Rectangle();

console.log('Is rect an instance of Rectangle? ' + (rect instanceof Rectangle)); // true
console.log('Is rect an instance of Shape? ' + (rect instanceof Shape)); // true
rect.move(1, 1); // Outputs, 'Shape moved.'
{% endcode %}

multiple inheritance:

{% code lang:js %}
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}

MyClass.prototype = Object.create(SuperClass.prototype); // inherit
mixin(MyClass.prototype, OtherSuperClass.prototype); // mixin

MyClass.prototype.myMethod = function() {
  // do a thing
};
{% endcode %}

Object.create()的一些用法：

{% code lang:js %}
var o;

// create an object with null as prototype
o = Object.create(null);


o = {};
// is equivalent to:
o = Object.create(Object.prototype);


// Example where we create an object with a couple of sample properties.
// (Note that the second parameter maps keys to *property descriptors*.)
o = Object.create(Object.prototype, {
  // foo is a regular 'value property'
  foo: { writable: true, configurable: true, value: 'hello' },
  // bar is a getter-and-setter (accessor) property
  bar: {
    configurable: false,
    get: function() { return 10; },
    set: function(value) { console.log('Setting `o.bar` to', value); }
/* with ES5 Accessors our code can look like this
    get function() { return 10; },
    set function(value) { console.log('setting `o.bar` to', value); } */
  }
});


function Constructor() {}
o = new Constructor();
// is equivalent to:
o = Object.create(Constructor.prototype);
// Of course, if there is actual initialization code in the
// Constructor function, the Object.create() cannot reflect it


// create a new object whose prototype is a new, empty object
// and a adding single property 'p', with value 42
o = Object.create({}, { p: { value: 42 } });

// by default properties ARE NOT writable, enumerable or configurable:
o.p = 24;
o.p;
// 42

o.q = 12;
for (var prop in o) {
  console.log(prop);
}
// 'q'

delete o.p;
// false

// to specify an ES3 property
o2 = Object.create({}, {
  p: {
    value: 42,
    writable: true,
    enumerable: true,
    configurable: true
  }
});
{% endcode %}

- Reference:
  1. [Object initializer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)
  2. [Object.create()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
- Related:
  1. [hasOwnProperty与for in](/blog/2014/11/06/js-hasownproperty-and-forin/)
