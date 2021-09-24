# 执行上下文
## 1. 分类
- `全局上下文`：最外层的上下文。根据实现的宿主环境，表示全局上下文的对象可能不一样。在浏览器中，全局上下文就是我们常说的 `window` 对象。上下文在其所有代码都执行完毕后会被销毁，包括定义在它上面的所有变量和函数（全局上下文在应用程序退出前才会被销毁，比如关闭网页或退出浏览器）
- `函数上下文`：每个函数调用都有自己的上下文。当代码执行流进入函数时，函数的上下文被推到一个上下文栈上。在函数执行完之后，上下文栈会弹出该函数上下文，将控制权返还给之前的执行上下文。`JavaScript` 程序的执行流就是通过这个上下文栈进行控制的。
## 2. 属性
当 `JavaScript` 代码执行一段 `可执行代码`（全局代码、函数代码、eval代码）时，会创建对应的执行上下文

对于每个执行上下文，都有三个重要属性：
- 变量对象(Variable object，VO)
- 作用域链(Scope chain)
- [this](JavaScript-this绑定.md)

### 2.1 变量对象
执行上下文中定义的所有变量和函数都存在于这个对象上。虽然无法通过代码访问变量对象，但后台处理数据会用到它。

#### 2.1.1 全局上下文
所有通过 `var` 定义的全局变量和函数都会成为 `全局上下文` 的属性和方法。使用 `let` 和 `const` 的顶级声明不会定义在 `全局上下文` 中，但在作用域链解析上效果是一样的。
#### 2.1.2 函数上下文
如果上下文是函数，则其 `活动对象（activation object）` 用作变量对象。  
`活动对象` 最初只有一个定义变量：`arguments`（全局上下文中没有这个变量）

### 2.2 作用域链
- 代码正在执行的上下文的 `变量对象` 始终位于作用域链的最前端。上下文中的代码在执行的时候，会创建变量对象的一个 `作用域链（scope chain）`。这个 `作用域链` 决定了各级上下文中的代码在访问变量和函数时的顺序。  
- `作用域链` 中的下一个 `变量对象` 来自包含上下文，再下一个 `变量对象` 来自再下一个包含上下文。以此类推直至全局上下文；全局上下文的 `变量对象` 始终是 `作用域链` 的最后一个变量对象

## 3. 执行过程
执行上下文的代码会分成三个阶段进行处理：`创建`、`分析` 和 `执行`，也可以叫做：
- 函数创建
- 进入执行上下文
- 代码执行

### 3.1 函数创建
函数的 `作用域` 在 `函数定义` 的时候就决定了  
这是因为函数有一个内部属性 `[[scope]]`，当函数创建的时候，就会保存所有 `父变量对象` 到其中，可以理解为 `[[scope]]` 就是所有 `父变量对象` 的层级链  

注意：`[[scope]]` 并不代表完整的作用域链！
### 3.2 进入执行上下文
当进入执行上下文时，这时候还没有执行代码，

`活动对象(AO)` 会包括：

1. 函数的所有形参 (如果是函数上下文)
   - 由名称和对应值组成的一个变量对象的属性被创建
   - 没有实参，属性值设为 `undefined`
2. 函数声明
   - 由名称和对应值（函数对象）组成一个变量对象的属性被创建
   - 如果变量对象已经存在相同名称的属性，则 `完全替换` 这个属性
3. 变量声明
   - 由名称和对应值（undefined）组成一个变量对象的属性被创建
   - 如果变量名称跟已经声明的 `形式参数` 或 `函数` 相同，则变量声明 `不会干扰` 已经存在的这类属性

创建 `VO/AO` 后，就会将 `活动对象(AO)` 添加到 `作用域` 的前端  
这时候执行上下文的 `作用域链` ，我们命名为 `Scope`：
```JavaScript
Scope = [AO].concat([[Scope]])
```
至此，`作用域链` 创建完毕
### 3.3 代码执行
在代码执行阶段，会顺序执行代码，根据代码，修改 `变量对象` 的值

### 3.4 总结 && 实例
**总结：**

1. 全局上下文的变量对象初始化是全局对象
2. 函数的 `作用域` 在定义时就决定了，内部属性 `[[scope]]`
3. 函数上下文的变量对象初始化只包括 `Arguments` 对象
4. 在进入执行上下文时会给变量对象添加形参、函数声明、变量声明等初始的属性值
5. 创建 `AO` 后，就会将 `活动对象(AO)` 添加到 `作用域` 的前端，这时候执行上下文的 `作用域链Scope` 生成
6. 在代码执行阶段，会再次修改变量对象的属性值

**分析代码执行过程：**
```JavaScript
var scope = "global scope"
function checkscope(){
    var scope = "local scope"
    function f(){
        return scope
    }
    return f
}
checkscope()() // local scope
```
1. 执行全局代码，创建全局执行上下文，全局执行上下文压入执行上下文栈
    ```JavaScript
    globalContext = {
        VO: {
          global,
          scope: 'global scope'
        },
        Scope: [globalContext.VO],
        this: globalContext.VO
    }

    ECStack = [
        globalContext
    ]
    ```
2. 执行 checkscope 函数，创建 checkscope 函数执行上下文，checkscope 执行上下文被压入执行上下文栈
    ```JavaScript
    checkscopeContext = {
        AO: {
            arguments: {
                length: 0
            },
            scope: 'local scope',
            f: function f(){}
        },
        Scope: [AO, globalContext.VO],
        this: undefined
    }

    ECStack = [
        checkscopeContext,
        globalContext
    ]
    ```
3. checkscope 函数执行完毕，checkscope 执行上下文从执行上下文栈中弹出
    ```JavaScript
    ECStack = [
        globalContext
    ]
    ```
4. 执行 f 函数，创建 f 函数执行上下文，f 执行上下文被压入执行上下文栈
    ```JavaScript
    fContext = {
        AO: {
            arguments: {
                length: 0
            }
        },
        Scope: [AO, checkscopeContext.AO, globalContext.VO],
        this: undefined
    }

    ECStack = [
        fContext,
        globalContext
    ]
    ```
5. f 函数执行完毕，f 函数上下文从执行上下文栈中弹出
    ```JavaScript
    ECStack = [
        globalContext
    ]
    ```