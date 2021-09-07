# this 全面解析
## 一、this到底是什么
`this` 是在 `运行` 时进行绑定的，并不是在 `编写` 时绑定，它的上下文取决于函数调用时的各种条件。`this` 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。  

当一个函数被调用时，会创建一个活动记录（也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。`this` 就是记录的其中一个属性，会在函数执行的过程中用到
## 二、绑定规则

`this`的绑定规则总共有下面5种

1. [默认绑定（严格/非严格模式）](#21-默认绑定)
2. [隐式绑定](#22-隐式绑定)
3. [显式绑定](#23-显式绑定)
4. [new绑定](#24-new绑定)
5. [箭头函数绑定](#25-箭头函数绑定)

### 2.1 默认绑定
- 独立函数调用，可以把默认绑定看作是无法应用其他规则时的默认规则，`this` 指向全局对象
- 严格模式下，不能将全局对象用于默认绑定，`this` 会绑定到`undefined`。只有函数运行在非严格模式下，默认绑定才能绑定到全局对象。在严格模式下调用函数则不影响默认绑定。

```JavaScript
function foo() { // 运行在严格模式下，this会绑定到undefined
    "use strict"
    console.log(this.a)
}

var a = 2

// 调用
foo(); // TypeError: Cannot read property 'a' of undefined

// --------------------------------------

function foo() { // 运行
    console.log( this.a );
}

var a = 2;

(function() { // 严格模式下调用函数则不影响默认绑定
    "use strict"
    foo() // 2
})()
```

### 2.2 隐式绑定
当函数引用有上下文对象时，隐式绑定规则会把函数中的 `this` 绑定到这个上下文对象。对象属性引用链中只有上一层或者说最后一层在调用中起作用。


```JavaScript
function foo() {
    console.log( this.a )
}

var obj2 = {
    a: 42,
    foo: foo
}

var obj1 = {
    a: 2,
    obj2: obj2
}

obj1.obj2.foo() // 42
```

#### 2.2.1 隐式丢失

被隐式绑定的函数特定情况下会丢失绑定对象，应用 `默认绑定`，把 `this` 绑定到 `全局对象` 或者 `undefined` 上。


```JavaScript
function foo() {
    console.log( this.a )
}
var obj = {
    a: 2,
    foo: foo
}
// 虽然bar是obj.foo的一个引用，但是实际上，它引用的是foo函数本身。
var bar = obj.foo // 函数别名
var a = "oops, global" // a是全局对象的属性

// bar()是一个不带任何修饰的函数调用，应用默认绑定。
bar() // "oops, global"
```

参数传递就是一种 `隐式赋值`，传入函数时也会被隐式赋值。  
回调函数丢失 `this绑定` 是非常常见的。

```JavaScript
function foo() {
    console.log( this.a )
}
function doFoo(fn) {
    // fn其实引用的是foo
    fn() // <-- 调用位置！
}
var obj = {
    a: 2,
    foo: foo
}
var a = "oops, global" // a是全局对象的属性

doFoo(obj.foo) // "oops, global"

// ----------------------------------------
// JS环境中内置的setTimeout()函数实现和下面的伪代码类似：
function setTimeout(fn, delay) {
    // 等待delay毫秒
    fn() // <-- 调用位置！
}
```
### 2.3 显式绑定
通过`call()` 或者 `apply()`方法。第一个参数是一个对象，在调用函数时将这个对象绑定到 `this`。因为直接指定 `this` 的绑定对象，称之为显示绑定。


```JavaScript
function foo() {
    console.log( this.a )
}
var obj = {
    a: 2
}

foo.call( obj ) // 2  调用foo时强制把foo的this绑定到obj上
```

`显示绑定` 无法解决丢失绑定问题。

#### 2.3.1 硬绑定

```JavaScript
function foo() {
    console.log( this.a )
}
var obj = {
    a: 2
}
var bar = function() {
    // 强制把foo的 this 绑定到了obj
    foo.call( obj )
}

bar() // 2
setTimeout(bar, 100) // 2
bar.call( window ) // 2 硬绑定的bar不可能再修改它的this
```
由于 `硬绑定` 是一种非常常用的模式，所以在 ES5 中提供了内置的方法 `Function.prototype.bind` 会返回一个硬编码的新函数，它会把参数设置为 `this` 的上下文并调用原始函数
#### 2.3.2 API调用的“上下文”
JavaScript 许多内置函数提供了一个可选参数，被称之为“上下文”（context），其作用和 `bind` 一样，确保回调函数使用指定的 `this`。这些函数实际上通过 `call` 和 `apply` 实现了显式绑定。

```JavaScript
function foo(el) {
	console.log(el, this.id)
}
var obj = {
    id: "awesome"
}

var myArray = [1, 2, 3]
// forEach 第二个参数 当执行回调函数 callback 时，用作 this 的值。
myArray.forEach(foo, obj) // 1 awesome 2 awesome 3 awesome
```
### 2.4 new绑定
在 JavaScript 中，构造函数只是被 `new` 操作符调用的普通函数而已，实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”

使用 `new` 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作：
1. 创建（或者说构造）一个全新的对象
2. 这个新对象会被执行 [[ 原型 ]] 连接
3. 这个新对象会绑定到函数调用的 this
4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象

```JavaScript
function foo(a) {
    this.a = a
}

var bar = new foo(2) // bar和foo(..)调用中的this进行绑定
console.log(bar.a) // 2
```
使用 `new` 来调用 `foo(..)` 时，我们会构造一个新对象并把它绑定到 `foo(..)` 调用中的 `this`
上。

### 2.5 箭头函数绑定
ES6 新增一种特殊函数类型：箭头函数，箭头函数无法使用上述四条规则，而是根据外层（**函数**或者**全局**）作用域（词法作用域）来决定 `this`。  
也就是说，箭头函数的 `this` 是在 `创建函数` 时确定，而不是在 `运行时` 确定


```JavaScript
function foo() {
    // 返回一个箭头函数
    // this 继承自 foo()
    return () => console.log(this.a)

    // ES6 之前实现
    var self = this // lexical capture of this
    return function() {
      console.log(self.a)
    }
}
var obj1 = {
    a: 2
}
var obj2 = {
    a: 3
}

var bar = foo.call(obj1)
bar.call(obj2) // 2，不是3！
```
### 3 优先级
箭头函数 > new绑定 > 显示绑定 > 隐式绑定 > 默认绑定

### 4 绑定例外
#### 4.1 被忽略的this
把 `null` 或者 `undefined` 作为 `this` 的绑定对象传入 `call`、`apply` 或者 `bind`，这些值在调用时会被忽略，实际应用的是 `默认规则`

下面两种情况下会传入 `null`

1. 使用`apply(..)`来“展开”一个数组，并当作参数传入一个函数
1. `bind(..)`可以对参数进行柯里化（预先设置一些参数）


```JavaScript
function foo(a, b) {
    console.log( "a:" + a + "，b:" + b );
}

// 把数组”展开“成参数
foo.apply(null, [2, 3]) // a:2，b:3

// 使用bind(..)进行柯里化
var bar = foo.bind(null, 2)
bar(3) // a:2，b:3
```

总是传入 `null` 来忽略 `this` 绑定可能产生一些副作用。如果某个函数确实使用了 `this` ，那默认绑定规则会把 `this` 绑定到全局对象中

##### 更安全的this

安全的做法就是传入一个特殊的对象（空对象），把 `this` 绑定到这个对象不会对你的程序产生任何副作用

JS中创建一个空对象最简单的方法是`Object.create(null)`，这个和 `{}` 很像，但是并不会创建 `Object.prototype` 这个委托，所以比 `{}` 更空


```JavaScript
function foo(a, b) {
    console.log( "a:" + a + "，b:" + b );
}

// 我们的空对象
var ø = Object.create( null )

// 把数组”展开“成参数
foo.apply(ø, [2, 3]) // a:2，b:3

// 使用bind(..)进行柯里化
var bar = foo.bind( ø, 2 )
bar(3) // a:2，b:3
```

#### 4.2 间接引用
间接引用下，调用这个函数会应用 `默认绑定` 规则。间接引用最容易在赋值时发生。

```JavaScript
function foo() {
    console.log( this.a )
}

var a = 2
var o = { a: 3, foo: foo }
var p = { a: 4}

o.foo() // 3
// p.foo = o.foo的返回值是目标函数的引用，所以调用位置是foo()
(p.foo = o.foo)() // 2
```

### 5 思考题
#### 5.1 题目一

```JavaScript
var num = 1
var myObject = {
    num: 2,
    add: function() {
        this.num = 3;
        (function() {
            console.log(this.num);
            this.num = 4;
        })()
        console.log(this.num)
    },
    sub: function() {
        console.log(this.num)
    }
}
myObject.add() // 1 3
console.log(myObject.num) // 3
console.log(num) // 4
var sub = myObject.sub
sub() // 4
```

#### 5.2 题目二

```JavaScript
var name = 'window'

var person1 = {
  name: 'person1',
  show1: function () {
    console.log(this.name)
  },
  show2: () => console.log(this.name),
  show3: function () {
    return function () {
      console.log(this.name)
    }
  },
  show4: function () {
    return () => console.log(this.name)
  }
}
var person2 = { name: 'person2' }

person1.show1() // person1，隐式绑定，this指向调用者 person1 
person1.show1.call(person2) // person2，显式绑定，this指向 person2

person1.show2() // window，箭头函数绑定，this指向外层作用域，即全局作用域
person1.show2.call(person2) // window，箭头函数绑定，this指向外层作用域，即全局作用域

person1.show3()() // window，默认绑定，这是一个高阶函数，调用者是window
person1.show3().call(person2) // person2，显式绑定，this指向 person2
person1.show3.call(person2)() // window，默认绑定，调用者是window

person1.show4()() // person1，箭头函数绑定，this指向外层作用域，即person1所在函数作用域
person1.show4().call(person2) // person1，箭头函数绑定
person1.show4.call(person2)() // person2，箭头函数 this 指向 person2 词法作用域
```

#### 5.3 题目三

```JavaScript
// 1、赋值语句是右执行的,此时会先执行右侧的对象
var obj = {
    // 2、say 是立即执行函数
    say: function() {
        function _say() {
            // 5、输出 window
            console.log(this);
        }
        // 3、编译阶段 obj 赋值为 undefined
        console.log(obj);
        // 4、obj是 undefined，bind 接收 undefined 会绑定到 window。
        return _say.bind(obj);
    }()
}
obj.say(); // undefined window
```
