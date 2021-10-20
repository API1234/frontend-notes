- [treeToArray](#treetoarray)
- [arrayToTree](#arraytotree)
- [loadsh._get(obj, path, defaultValue)](#loadsh_getobj-path-defaultvalue)
- [Array.flat(arr, deep)](#arrayflatarr-deep)
- [String.prototype.replace()](#stringprototypereplace)
  - [camelize(str)](#camelizestr)
  - [objReplace(str, obj)](#objreplacestr-obj)
## treeToArray
```JavaScript
function treeToArray(tree, key = 'sub') {
  const res = []

  const dps = tree => {
    tree.forEach(item => {
      res.push(item)
      item[key] && dps(item[key])
    })
  }

  dps(tree)
  return res
}
```

## arrayToTree
```JavaScript
function arrayToTree(arr) {
  const result = arr.reduce((acc, cur) => {
    acc[cur.pid] ? acc[cur.pid].push(cur) : acc[cur.pid] = [cur]
    return acc
  }, {})

  Object.keys(result).forEach(key => {
    result[key].forEach(item => item.children = result[item.id])
  })

  return result[0]
}

/** 测试数组 */
const dataArr = [
  {id: 1, name: '1', pid: 0}, 
  {id: 2, name: '2', pid: 0}, 
  {id: 3, name: '3', pid: 0}, 
  {id: 11, name: '1_1', pid: 1}, 
  {id: 12, name: '1_2', pid: 1}, 
  {id: 21, name: '2_1', pid: 2}, 
  {id: 22, name: '2_2', pid: 2}, 
  {id: 31, name: '3_1', pid: 3}, 
  {id: 32, name: '3_2', pid: 3}, 
  {id: 111,name: '1_1_1', pid: 11},
  {id: 211,name: '2_1_1', pid: 21},
  {id: 311,name: '3_1_1', pid: 31}
]
```

## loadsh._get(obj, path, defaultValue)
```JavaScript
function _get(obj, path, defaultValue) {
  let newPath = Array.isArray(path)
    ? path
    : path.replace(/\[/g, '.').replace(/\]/g, '').split('.')

  let res = newPath.reduce((o, k) => (o || {})[k], obj)
  return res === undefined ? defaultValue : res
}

/** 测试代码 */
var obj = { 'a': [{ 'b': { 'c': 'test' } }] };
console.log(_get(obj, 'a[0].b.c', 'defaultValue'))
```

## Array.flat(arr, deep)
```JavaScript
/** Recursive solution */
function flat(arr, depth = 1) {
   return depth > 0
    ? arr.reduce((acc, val) => acc.concat(Array.isArray(val) ? flat(val, d - 1) : val), [])
    : arr.slice()
}


/** Iterative solution */
function flat(arr, depth = 1) {
  const result = []
  const stack =  [...arr.map(item => [item, depth])]

  while (stack.length) {
    const [item, depth] = stack.pop()
    if (Array.isArray(item) && depth) {
      stack.push(...item.map(v => [v, depth - 1]))
    } else {
      result.push(item)
    }
  }

  return result.reverse()
}
```

## String.prototype.replace()
**语法：**  
> str.replace(regexp|substr, newSubStr|function)

**参数**  
- **regexp** (pattern)  
一个RegExp 对象或者其字面量。该正则所匹配的内容会被第二个参数的返回值替换掉
- **substr** (pattern)  
一个将被 newSubStr 替换的 字符串。其被视为一整个字符串，而不是一个正则表达式。仅第一个匹配项会被替换
- **newSubStr** (replacement)  
用于替换掉第一个参数在原字符串中的匹配部分的字符串。该字符串中可以内插一些**特殊的变量名**
  |  变量名   | 代表的值  |
  |  :----:  | :----:  |
  | $$  | 插入一个 "$" |
  | $&  | 插入匹配的子串 |
  | $`  | 插入当前匹配的子串左边的内容 |
  | $'  | 插入当前匹配的子串右边的内容 |
- **function** (replacement)  
一个用来创建新子字符串的函数，该函数的返回值将替换掉第一个参数匹配到的结果
  - 如果第一个参数是 `正则` 而且有`()`匹配  
    - 参数1：匹配到的字符串  
    - 参数2：正则中 `()` 捕获的内容  
    - 参数3：匹配到的字符串第一个字符的下标  
    - 参数4：str 全字符串
  - 否则   
    - 参数1：匹配到的字符串  
    - 参数2：匹配到的字符串第一个字符的下标  
    - 参数3：str 全字符串

### camelize(str)
```JavaScript
/**
 * Camelize a hyphen-delimited string.
 * aaa-bbb-ccc => aaaBbbCcc
 */
const camelizeRE = /-(\w)/g

function camelize(str) {
  return str.replace(camelizeRE, (_, c) => c ? c.toUpperCase() : '')
}

```
### objReplace(str, obj)
```JavaScript
/** 将对象的值替换str中的占位符 */
function objReplace(str, obj) {
  return str.replace(
    /{(\w+)}/g,
    (s, key) => obj[key] === undefined ? s : obj[key])
}

/** 测试代码 */
let a = 'my name is {name}, my age is {age}'
let b = {
  name: 'shpi',
  age: '24'
}
console.log(objReplace(a,b))
```