## 防抖函数（debounce）

```JavaScript
function debounce(func, wait, option = { leading: false, trailing: true }) {
  let timer = null

  return function(...args) {
    let isInvoked = false // leading = trailing = true 时，一次事件不应该被触发两次

    if (option.leading && timer === null) {
      func.apply(this, args)
      isInvoked = true
    }

    timer && clearTimeout(timer)
    timer = setTimeout(() => {
      if (option.trailing && !isInvoked) {
        func.apply(this, args)
      }
      timer = null
    }, wait)
  }
}
```
## 节流函数（throttle）

```JavaScript
function throttle(func, wait, option = {leading: true, trailing: true}) {
  if (!option.leading && !option.trailing) return () => null
  let waiting = false
  let lastArgs

  const timeoutFn = (context) => {
    setTimeout(() => {
      if (option.trailing && lastArgs) {
        func.apply(context, lastArgs)
        lastArgs = null
        timeoutFn(context)
      } else {
        waiting = false
      }
    }, wait)
  }
  
  return function (...args) {
    if (!waiting) {
      waiting = true
      if (option.leading) func.apply(this, args)
      timeoutFn(this)
    } else {
      lastArgs = args
    }
  }
}
```

## 代码测试
```JavaScript
/** 测试代码 */
const run = (input) => {
  const startTime = new Date().getTime()
  const calls = []

  const func = (arg) => {
    calls.push(`${arg}@${new Date().getTime() - startTime}`)
  }
  const throttled = throttle(func, 2000, { leading: true, trailing: true })

  input.forEach(call => {
     const [arg, time] = call.split('@')
     setTimeout(() => {
       throttled(arg)
      }, time)
  })
  return calls
}

const res = run(['A@0', 'B@3000', 'C@4000', 'D@4500', 'E@5600', 'F@6700'])
setTimeout(() => {
  console.log(res)
}, 10000)
```