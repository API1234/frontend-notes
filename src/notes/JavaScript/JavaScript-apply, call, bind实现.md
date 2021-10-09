# apply, call, bind 区别 && 实现
## Function.prototype.apply()
### 语法
```JavaScript
func.apply(thisArg, [argsArray])
```
### 参数
- `thisArg`  `可选`  
在 `func` 函数运行时使用的 `this` 值。如果这个函数处于 `非严格模式` 下，则指定为 `null` 或 `undefined` 时会自动替换为指向 `全局对象`，原始值会被包装
- `argsArray`  `可选`  
一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 `func` 函数。如果该参数的值为 `null` 或 `undefined`，则表示不需要传入任何参数

### 返回值
调用有指定 `this` 值和参数的函数的结果，若该方法没有返回值，则返回 `undefined`

### apply 实现
```JavaScript
Function.prototype.apply = function (context, arr) {
  context = context ? Object(context) : window
  const fn = Symbol()
  context[fn] = this
  const result = arr ? context[fn](...arr) : context[fn]()
  delete context[fn]
  return result
}
```
## Function.prototype.call()
### 语法
```JavaScript
func.call(thisArg, arg1, arg2, ...)
```
### 参数
- `thisArg`  `可选`  
在 `func` 函数运行时使用的 `this` 值。如果这个函数处于 `非严格模式` 下，则指定为 `null` 或 `undefined` 时会自动替换为指向 `全局对象`，原始值会被包装
- `arg1, arg2, ...`  `可选`  
指定的参数列表

### 返回值
调用有指定 `this` 值和参数的函数的结果，若该方法没有返回值，则返回 `undefined`

### call 实现
```JavaScript
Function.prototype.call = function(context, ...args) {
  context = context ? Object(context) : window
  const fn = Symbol()
  context[fn] = this
  const res = context[fn](...args)
  delete context[fn]
  return res
}
```
## Function.prototype.bind()
### 语法
```JavaScript
func.bind(thisArg[, arg1[, arg2[, ...]]])
```
### 参数
- `thisArg`  `可选`  
  - 调用绑定函数时作为 `this` 参数传递给目标函数的值
  - 如果使用 `new` 运算符构造绑定函数，则忽略该值
  - 作为 `thisArg` 传递的任何原始值都将转换为 `object`
  - 如果 `thisArg` 是 `null `或 `undefined`，**执行作用域** 的 `this` 将被视为新函数的 `thisArg`
    - 使用 `new` 时，执行作用域指 `func.prototype`
    - 不使用 `new` 时，执行作用域指 `全局对象`
- `arg1, arg2, ...`  `可选`  
当目标函数被调用时，被预置入绑定函数的参数列表中的参数

### 返回值
返回一个原函数的拷贝，并拥有指定的 `this` 值和 `初始参数`，没有 `prototype` 属性

### bind 实现
#### 不考虑 new
```JavaScript
Function.prototype.bind = function(contenxt, ...args) {
  const bound = (...newArgs) => {
    return this.apply(contenxt, args.concat(newArgs))
  }
  return bound
}
```
#### [考虑 new](https://github.com/yygmind/blog/issues/23)
```JavaScript
Function.prototype.bind = function (context, ...args) {
  if (typeof this !== 'function') throw new Error('error')

  const self = this

  return function Fn(...newArgs) {
    // 因为返回了一个函数，我们可以 new Fn()，所以需要判断
    if (this instanceof Fn) {
      return new self(...args, ...newArgs)
    }
    return self.apply(context, [...args, ...newArgs])
  }
}
```