# Functions
## Arrow Functions
### 定义
```
let fun = () => {}

typeof func               // "function"
func instanceof Function  // true
```

### 箭头函数和普通函数的区别
1. 箭头函数内部的变量：`this`，`super`，`arguments`，`new.target`，与包含该箭头函数的最近非箭头函数相关联
  * `this`在箭头函数声明时被指定，并且**不能被更改**，在函数的整个生命周期中保持不变
  * `arguments`变量不存在
2. 箭头函数不能作为构造函数（因为箭头函数没有`[[Construct]]`方法）
3. 箭头函数没有`prototype`属性
4. 箭头函数的参数的命名不能重复
```
var aa = (a, a) => {} // Uncaught SyntaxError: Duplicate parameter name not allowed in this context
```

## 尾调用（Tail Call）
1. 定义：函数的最后一个执行语句是一个函数调用。
```
function doSomething() {
  return doSomethingElse();   // tail call
}
```

2. 存在的问题：栈溢出
3. ES6对尾调用的优化
