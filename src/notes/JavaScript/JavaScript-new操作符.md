### 一、什么是 new？
`new` 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例

**语法**
```JavaScript
new constructor[([arguments])]
```

- `constructor`  
一个指定对象实例的类型的类或函数
- `arguments`  
一个用于被 `constructor` 调用的参数列表
### 二、new 做了什么？
new 关键字会进行如下的操作：

1. 创建一个空的简单 `JavaScript` 对象（即 `{}`），并链接至构造函数的 `原型对象`
2. 将 步骤1 新创建的对象作为 `this` 的上下文
3. 如果构造函数没有返回对象，则返回 `this`，否则返回构造函数返回的对象
### 三、实现 new
```JavaScript
function create(constructor, ...args) {
  // 1、创建一个空的对象 obj， 并链接到构造函数的原型对象
  const obj = Object.create(constructor.prototype)

  // 2、绑定 this 实现继承，obj 可以访问到构造函数中的属性
  const returnObj = constructor.apply(obj, args)

  // 3、优先返回构造函数返回的对象
  return returnObj instanceof Object ? returnObj : obj
}


/** 测试代码 */
function Person(name, age) {
  this.name = name
  this.age = age
  this.say = function() {
    console.log(name + ' - ' + age)
  }
}
const shpi = create(Person, 'shpi', 24)
shpi.say() // shpi - 24
```