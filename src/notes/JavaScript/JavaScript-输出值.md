- [Promise 相关](#promise-相关)
- [执行上下文与作用域相关](#执行上下文与作用域相关)
- [原型链](#原型链)
- [块级作用域](#块级作用域)
  - [问题一：改变下边代码输出为 0 1 2 3 4](#问题一改变下边代码输出为-0-1-2-3-4)
- [other](#other)
  - [问题一：连续赋值](#问题一连续赋值)
  - [问题二：对象的 key](#问题二对象的-key)
  - [问题三：函数入参（引用类型）](#问题三函数入参引用类型)
## [Promise 相关](./JavaScript-Promise.md#2-promise-相关题目) 

## [执行上下文与作用域相关](./JavaScript-执行上下文与作用域.md#4-相关题目)

## [原型链](../JavaScript/JavaScript-原型链.md#相关题目)
## 块级作用域
### 问题一：改变下边代码输出为 0 1 2 3 4
```JavaScript
for (var i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i)
  }, 0)
}

// 5 5 5 5 5
```
**方法一：**  
利用 `setTimeout` 函数的第三个参数，会作为回调函数的第一个参数传入
```JavaScript
for (var i = 0; i < 5; i++) {
  setTimeout(i => {
    console.log(i);
  }, 0, i)
}
```

**方法二：**  
利用 `let` 变量的特性 — 在每一次 for 循环的过程中，`let` 声明的变量会在当前的块级作用域里面（for 循环的 body 体，也即两个花括号之间的内容区域）创建一个文法环境（Lexical Environment），该环境里面包括了当前 for 循环过程中的 i
```JavaScript
for (let i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i);
  }, 0)
}

//等价于

for (var i = 0; i < 5; i++) {
  let _i = i
  // const _i = i
  // 用 var 是不行的
  setTimeout(() => {
    console.log(_i)
  }, 0)
}
```
**方法三：**  
- 利用函数自执行的方式，把当前 for 循环过程中的 i 传递进去，构建出块级作用域
- 利用其它方式构建出块级作用域

```JavaScript
for (var i = 0; i < 5; i++) {
  (i => {
    setTimeout(() => {
      console.log(i);
    }, 0)
  })(i)
}
```

## other
### 问题一：连续赋值
```JavaScript
var a = {n: 1};
var b = a;
a.x = a = {n: 2};

console.log(a) 	// { n: 2 }
console.log(b)  // { n: 1, x: { n: 2 } }
console.log(a.x)  // undefined
console.log(b.x)  // { n: 2 }
```
- `a.x` 即完成了 `x` 的声明，其值为 `undefined`
- 对象成员等待赋值时，锁定的赋值目标是成员，而非对象
- 对象重新赋值时，并非是修改原堆内存的值，而是重新分配堆内存，栈内存中的指针会做相应修改。（如果原堆内存有多个栈内存指向它，由于引用还存在，原堆内存的数据不会消失。如果堆内存再无其它引用，则会被JS的垃圾回收机制回收。对象的成员对象也一样。PS：引用类型应该都如此）

### 问题二：对象的 key
对象的 key 只能为 `string` || `symbol`，如果对象的 `key` 不为以上两种类型之一，则会调用 `toString()` 方法转为 `string`

```JavaScript
// example 1
var a={}, b='123', c=123
a[b]='b'
a[c]='c'
console.log(a[b])   // 'c'
// a = { '123': 'c' }

// example 2
var a={}, b=Symbol('123'), c=Symbol('123')
a[b]='b'
a[c]='c'
console.log(a[b])   // 'b'
// a = { Symbol(123): 'b', Symbol(123): 'c' }
// Symbol('123') === Symbol('123') // false
// Symbol相当于生成一个地址的引用

// example 3
var a={}, b={key:'123'}, c={key:'456'}
a[b]='b'
a[c]='c'
console.log(a[b])   // 'c'
// a = { [object Object]: "c" }
```

### 问题三：函数入参（引用类型）
```JavaScript
let a = [1,2,3]
let b = {}
function fn(a,b){
    a = []
    b.b = 2
    b = {a:1}
}
fn(a,b)
console.log(a) // [1, 2, 3]
console.log(b) // {b: 2}

// 对于 JS 的引用类型来说，
// 我们的复制实际上是复制它在堆内存中的地址，
// 而不是这个引用对象本身
```