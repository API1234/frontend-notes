# Promise 对象
- [Promise 对象](#promise-对象)
  - [1. Promise 特性 && 使用](#1-promise-特性--使用)
    - [1.1 Promise 特性](#11-promise-特性)
    - [1.2 Promise.prototype.then(onResolved, onRejected)](#12-promiseprototypethenonresolved-onrejected)
    - [1.3 Promise.prototype.catch(onRejected)](#13-promiseprototypecatchonrejected)
    - [1.4 Promise.prototype.finally(callback)](#14-promiseprototypefinallycallback)
    - [1.5 Promise.resolve(value)](#15-promiseresolvevalue)
    - [1.6 Promise.reject(reason)](#16-promiserejectreason)
    - [1.7 Promise.all()](#17-promiseall)
    - [1.8 Promise.race()](#18-promiserace)
    - [1.9 Promise.allSettled()](#19-promiseallsettled)
    - [1.10 Promise.any()](#110-promiseany)
  - [2. Promise 相关题目](#2-promise-相关题目)
    - [2.1 题目一](#21-题目一)
    - [2.2 题目二](#22-题目二)
    - [2.3 题目三](#23-题目三)
    - [2.4 题目四](#24-题目四)
  - [3. Promise 相关实现](#3-promise-相关实现)
    - [3.1 Promise 实现](#31-promise-实现)
    - [3.2 Promisify 实现](#32-promisify-实现)

## 1. Promise 特性 && 使用
### 1.1 Promise 特性
- `Promise` 的状态不受外界影响。`Promise` 代表一个异步操作，有三种状态：`pending`（进行中）、`fulfilled`（已成功）和 `rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise` 对象的状态改变，只有两种可能：从 `pending` 变为 `fulfilled` 和从 `pending` 变为 `rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 `resolved`（已定型）。如果改变已经发生了，你再对 `Promise` 对象添加回调函数，也会立即得到这个结果
### 1.2 Promise.prototype.then(onResolved, onRejected)  
- `onFulfilled` `可选`  
当 `Promise` 变成 `fulfilled` 时调用的函数。该函数有一个参数，即接受的 `最终结果`（the fulfillment  value）。如果该参数不是函数，则会在内部被替换为 `x => x`，即原样返回 `promise` 最终结果的函数
- `onRejected` `可选`  
当 `Promise` 变成 `rejected` 时调用的函数。该函数有一个参数，即 `拒绝原因`（rejection reason）。如果该参数不是函数，则会在内部被替换为 `reason => { throw reason }`

返回一个新的 `Promise` 实例，可以链式调用，前一个 `then` 的 `return值` 会作为下一个 `then` 回调函数的参数
### 1.3 Promise.prototype.catch(onRejected)
是 `Promise.prototype.then(null, onRejected)` 的别名，用于指定发生错误时的回调函数
### 1.4 Promise.prototype.finally(callback) 
1. `finally` 用于指定不管 `Promise` 对象最后状态如何，都会执行的操作
2. `finally` 的回调函数不接受任何参数，这表明 `finally` 方法里面的操作，应该是与状态无关的，不依赖于 `Promise` 的执行结果
3. `finally` 本质上是 `then` 方法的特例，而且 `finally` 方法总是会返回原来的值
   ```JavaScript
   // resolve 的值是 undefined
   Promise.resolve(2).then(() => {}, () => {})

   // resolve 的值是 2
   Promise.resolve(2).finally(() => {})

   // reject 的值是 undefined
   Promise.reject(3).then(() => {}, () => {})

   // reject 的值是 3
   Promise.reject(3).finally(() => {})
   ```
### 1.5 Promise.resolve(value)
将 `value` 转为 `Promise` 对象
1. `value` 是一个 `Promise` 实例  
`Promise.resolve` 将不做任何修改、原封不动地返回这个实例
1. `value` 是一个 `thenable`(即具有 `then` 方法) 对象  `Promise.resolve` 将 `该对象` 转为 `Promise` 对象并立即执行 `then` 方法
2. `value` 是原始值（包含 `undefined`），或不具有 `then` 方法的对象  
`Promise.resolve` 返回一个新的 `Promise` 对象，状态为`fulfilled`，`value` 将会作为 `then` 方法中 `onResolved` 回调函数的参数
### 1.6 Promise.reject(reason)
- `Promise.reject()` 也会返回一个新的 `Promise` 实例，该实例的状态为 `rejected`
- `Promise.reject()` 方法的参数 `reason`，会原封不动地作为 `reject` 的理由，变成后续方法的参数

### 1.7 Promise.all()
`Promise.all` 方法用于将多个 `Promise` 实例，包装成一个新的 `Promise` 实例
```JavaScript
const p = Promise.all([p1, p2, p3])
```
`Promise.all` 方法接受一个数组作为参数，`p1`、`p2`、`p3` 都是 `Promise` 实例，如果不是，就会先调用 `Promise.resolve` 方法，将参数转为 `Promise` 实例，再进一步处理。

`p` 的状态由`p1`、`p2`、`p3` 决定，分成两种情况：
1. 只有 `p1`、`p2`、`p3` 的状态都变成 `fulfilled`，`p` 的状态才会变成 `fulfilled`，此时 `p1`、`p2`、`p3` 的 `返回值` 组成一个数组，传递给 `p` 的回调函数
2. 只要`p1`、`p2`、`p3` 之中有一个被 `rejected`，`p` 的状态就变成 `rejected`，此时 `第一个` 被 `reject` 的实例的返回值，会传递给 `p` 的回调函数

**注意**，如果作为参数的 `Promise` 实例，自己定义了 `catch` 方法，那么它一旦被 `rejected` ，并不会触发 `Promise.all` 的 `catch` 方法

```JavaScript
const p1 = new Promise((resolve, reject) => {
  resolve('hello')
})
.then(result => result)
.catch(e => e)

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了')
})
.then(result => result)
.catch(e => e)

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e))
// ["hello", Error: 报错了]
```
上面代码中，`p1` 会 `resolved`，`p2` 首先会 `rejected`，但是 `p2` 有自己的 `catch` 方法，该方法返回的是一个新的  `Promise` 实例，`p2` 指向的实际上是这个实例。该实例执行完`catch` 方法后，也会变成 `resolved`，导致 `Promise.all` 方法参数里面的两个实例都会 `resolved`，因此会调用 `then` 方法指定的回调函数，而不会调用 `catch` 方法指定的回调函数

如果 `p2` 没有自己的 `catch` 方法，或 `catch` 返回 `rejected` 的 `Promise`，就会调用 `Promise.all` 的 `catch` 方法

```JavaScript
const p1 = new Promise((resolve, reject) => {
  resolve('hello')
})
.then(result => result);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了')
})
.then(result => result)
.catch(e => { throw 'catch' })

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e))
// catch
```

### 1.8 Promise.race()
`Promise.race` 方法同样是将多个 `Promise` 实例，包装成一个新的 `Promise` 实例

```JavaScript
const p = Promise.race([p1, p2, p3])
```
上面代码中，只要`p1`、`p2`、`p3` 之中有一个实例率先改变状态，`p` 的状态就跟着改变。那个 `率先` 改变的 `Promise` 实例的 `返回值`，就传递给 `p` 的回调函数

`Promise.race` 方法的参数与 `Promise.all` 方法一样，如果不是 `Promise` 实例，就会先调用 `Promise.resolve` 方法，将参数转为 `Promise` 实例，再进一步处理
### 1.9 Promise.allSettled()
`Promise.allSettled` 方法，用来确定一组异步操作是否都结束了（不管成功或失败）。所以，它的名字叫做 `"Settled"`，包含了 `fulfilled` 和 `rejected` 两种情况

```JavaScript
const resolved = Promise.resolve(42)
const rejected = Promise.reject(-1)
const val = 123

const allSettledPromise = Promise.allSettled([resolved, rejected, val])

allSettledPromise.then(function (result) {
  console.log(result)
})
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 },
//    { status: 'fulfilled', reason: 123 },
// ]
```

- `Promise.allSettled` 方法接受一个数组作为参数，数组的每个成员都是一个 `Promise` 对象，如果不是，就会先调用 `Promise.resolve` 方法，将参数转为 `Promise` 实例。
- 等到参数数组的所有 `Promise` 对象都发生状态变更，返回一个新的 `Promise` 对象，状态总是 `fulfilled`。它的回调函数会接收到一个数组作为参数，该数组的每个成员对应前面数组的每个 `Promise` 对象
### 1.10 Promise.any()
- `Promise.any` 方法接受一个数组作为参数，数组的每个成员都是一个 `Promise` 对象，如果不是，就会先调用 `Promise.resolve` 方法，将参数转为 `Promise` 实例
- 只要参数实例有一个变成 `fulfilled` 状态，返回 `Promise` 就会变成 `fulfilled` 状态
- 如果所有参数实例都变成 `rejected` 状态，返回 `Promise` 就会变成 `rejected` 状态，抛出的错误，不是一个一般的 `Error` 错误对象，而是一个 `AggregateError` 实例  
  `AggregateError` 的实现示例：

  ```JavaScript
  // new AggregateError() extends Array

  const err = new AggregateError();
  err.push(new Error("first error"));
  err.push(new Error("second error"));
  // ...
  throw err;
  ```


## 2. Promise 相关题目
### 2.1 题目一

```JavaScript
const promise = new Promise((resolve, reject) => {
    console.log(1)
    resolve()
    console.log(2)
    reject('error')
})

promise
.then(() => console.log(3))
.catch(e => console.log(e))

console.log(4)

/**
 * 1
 * 2
 * 4
 * 3
 */
```

### 2.2 题目二

```JavaScript
const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
        console.log('once')
        resolve('success')
    }, 1000)
})

promise.then(res => console.log(res))

promise.then(res => console.log(res))

/**
 * 过一秒
 * once
 * success
 * success
 */
```

### 2.3 题目三

```JavaScript
const p1 = () => {
  new Promise((resolve, reject) => {
	console.log(1)
	let p2 = new Promise((resolve, reject) => {
		console.log(2)
		const timeOut1 = setTimeout(() => {
			console.log(3)
			resolve(4)
		}, 0)
		resolve(5)
	})

	resolve(6)
	p2.then(val => console.log(val))
  })
}

console.log('test')

const timeOut2 = setTimeout(() => {
	console.log(8)
	const p3 = new Promise(reject => {
		reject(9)
	})
  .then(res => console.log(res))
}, 0)


p1().then((val) => {
	console.log(val)
})
console.log(10)

/**
 * test
 * 1
 * 2
 * 10
 * 5
 * 6
 * 8
 * 9
 * 3
 */
```

### 2.4 题目四
写出下面函数的执行结果：

```JavaScript
Promise.resolve(1)
.then(2)
.then(Promise.resolve(3))
.then(console.log)

/**
 * Promise.resolve(1)会返回一个 Promise 对象并且会将 1 当做 then 的参数
 * then 或 catch 的参数期望是函数，传入非函数则会发生值穿透
 * 所以最后会输出：1
 */
```

## 3. Promise 相关实现
### 3.1 Promise 实现
```JavaScript
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

function Promise(executor) {
  const self = this
  self.status = PENDING
  self.onFulfilled = []
  self.onRejected = []

  function resolve(value) {
    if (self.status === PENDING) {
      self.status = FULFILLED
      self.value = value
      self.onFulfilled.forEach(fn => fn())
    }
  }

  function reject(reason) {
    if (self.status === PENDING) {
      self.status = REJECTED
      self.reason = reason
      self.onRejected.forEach(fn => fn())
    }
  }

  try {
    executor(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

function resolvePromise(_promise, x, resolve, reject) {
  if (_promise === x) {
    reject(new TypeError('Chaining cycle'))
  }
  if (x && typeof x === 'object' || typeof x === 'function') {
    let used
    try {
      let then = x.then
      if (typeof then === 'function') {
        then.call(x, value => {
          if (used) return
          used = true
          resolvePromise(_promise, value, resolve, reject)
        }, reason => {
          if (used) return
          used = true
          reject(reason)
        })
      } else {
        if (used) return
        used = true
        resolve(x)
      }
    } catch (e) {
      if (used) return
      used = true
      reject(e)
    }
  } else {
    resolve(x)
  }
}

Promise.prototype.then = function(onFulfilled, onRejected) {
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
  onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }
  const self = this

  const _promise = new Promise((resolve, reject) => {
    if (self.status === FULFILLED) {
      setTimeout(() => {
        try {
          let x = onFulfilled(self.value)
          resolvePromise(_promise, x, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    } else if (self.status === REJECTED) {
      setTimeout(() => {
        try {
          let x = onRejected(self.reason)
          resolvePromise(_promise, x, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    } else if (self.status === PENDING) {
      self.onFulfilled.push(() => {
        setTimeout(() => {
          try {
            let x = onFulfilled(self.value)
            resolvePromise(_promise, x, resolve, reject)
          } catch (e) {
            reject(e)
          }
        })
      })
      self.onRejected.push(() => {
        setTimeout(() => {
          try {
            let x = onRejected(self.reason)
            resolvePromise(_promise, x, resolve, reject)
          } catch (e) {
          reject(e)
          }
        })
      })
    }
  })

  return _promise
}

Promise.prototype.catch = function (onRejected) {
  return this.then(null, onRejected)
}

Promise.prototype.finally = function (callback) {
  const P = this.constructor
  return this.then(
    value  => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => { throw reason })
  )
}

/**
 * Creates a new resolved promise for the provided value.
 * @param { any } [value] The value the promise was resolved.
 * @returns { Promise } A new resolved Promise.
 */
Promise.resolve = function (param) {
  if (param instanceof Promise) {
    return param
  }

  return new Promise((resolve, reject) => {
    if (param && param.then && typeof param.then === 'function') {
      setTimeout(() => {
        param.then(resolve, reject)
      })
    } else {
      resolve(param)
    }
  })
}

/**
 * Creates a new rejected promise for the provided reason.
 * @param { any } [reason] The reason the promise was rejected.
 * @returns { Promise } A new rejected Promise.
 */
Promise.reject = function (reason) {
  return new Promise((resolve, reject) => {
    reject(reason);
  })
}

/**
 * Creates a Promise that is resolved with an array of results when all of the provided Promises resolve,
 * or rejected when any Promise is rejected.
 * @param { Promise[] } promises An array of Promises.
 * @returns { Promise } A new Promise.
 */
Promise.all = function(promises) {
  return new Promise((resolve, reject) => {
    const len = promises.length
    const values = []
    if (len === 0) return resolve(values)
    let count = 0

    for (let i = 0; i < len; i++) {
      Promise.resolve(promises[i]).then(value => {
        values[i] = value
        count++
        if (count === len) return resolve(values)
      }, reason => {
        return reject(reason)
      })
    }
  })
}

/**
 * Creates a Promise that is resolved or rejected when any of the provided Promises
 * are resolved or rejected.
 * @param { Promise[] } promises An array of Promises.
 * @returns { Promise } A new Promise.
 */
Promise.race = function(promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      Promise.resolve(promises[i]).then(value => {
        return resolve(value)
      }, reason => {
        return reject(reason)
      })
    }
  })
}

/**
 * Creates a Promise that is resolved with an array of results when
 * all of the provided Promises resolve or reject.
 * @param { Promise[] } promises An array of Promises.
 * @return { Promise } A new resolved Promise.
 */
Promise.allSettled = function(promises) {
  return new Promise((resolve, reject) => {
    const len = promises.length
    const values = []
    if (len === 0) return resolve(values)
    let count = 0
    promises.forEach((item, index) => {
      Promise.resolve(item)
      .then(
        value => { values[index] = { status: FULFILLED, value } },
        reason => { values[index] = { status: REJECTED, reason } }
      )
      .finally(() => {
        count++
        if (count === len) resolve(values)
      })
    })
  })
}

/**
 * Creates a Promise that is rejected with an array of results when all of the provided Promises reject,
 * or resolved when any Promise is rejected.
 * @param { Promise[] } promises An array of Promises.
 * @returns { Promise } A new Promise.
 */
Promise.any = function(promises) {
  return new Promise((resolve,reject) => {
    const len = promises.length
    const reasons = []
    if (len === 0) return reject(reasons)
    let count = 0

    for (let i = 0; i < len; i++) {
      Promise.resolve(promises[i]).then(value => {
        return resolve(value)
      }, reason => {
        count++
        reasons[i] = reason
        if (count === len) {
          return reject(reasons)
        }
      })
    }
  })
}

/**
 * npm install -g promises-aplus-tests
 * promises-aplus-tests promise.js
 */
Promise.defer = Promise.deferred = function () {
  let dfd = {}
  dfd.promise = new Promise((resolve, reject) => {
      dfd.resolve = resolve
      dfd.reject = reject
  });
  return dfd
}
module.exports = Promise
```

### 3.2 Promisify 实现
```JavaScript
// 使用条件限制
// 回调函数在主函数中的参数位置是最后一个
// 回调函数参数中的第一个参数是 error
function promisify(func) {
  return function(...args) {
    return new Promise((resolve, reject) => {
      const callback = (error, data) => {
        error ? reject(error) : resolve(data)
      }
      args.push(callback)
      func.apply(this, args)
    })
  }
}
```