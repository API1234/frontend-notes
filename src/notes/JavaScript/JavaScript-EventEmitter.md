```JavaScript
class EventEmitter {
  constructor() {
    this.events = {}
  }

  on(type, listener) {
    if (this.events[type]) {
      this.events[type].push(listener)
    } else {
      this.events[type] = [listener]
    }
  }

  emit(type, ...args) {
    if (this.events[type]) {
      // todo: once 和 on 同时存在时，once改变数组长度
      this.events[type].forEach(fn => fn.apply(this, args))
    }
  }

  off(type, listener) {
    if (this.events[type]) {
      const index = this.events[type].indexOf(listener)
      this.events[type].splice(index, 1)
    }
  }

  once(type, listener) {
    function _once(...args) {
      listener.apply(this, args)
      this.off(type, _once)
    }

    if (this.events[type]) {
      this.events[type].push(_once)
    } else {
      this.events[type] = [_once]
    }
  }
}


/** 测试用例 */
const event = new EventEmitter()

const say = function(str) {
  console.log(str);
}

event.once('say',say);
event.on('say',say);

event.emit('say','111');
event.emit('say','222');
event.emit('say','333');
```
