# Functions

# function
## Default Parameter Values
### in ES5
* 方法1：使用`||`判断参数是否提供(不安全，值为0时会被认为是false而使用默认值)
* 方法2：使用`typeof param !== "undefined"`判断参数是否提供

### in ES6
ES6的默认参数：未提供参数或者参数是`undefined`时，会使用默认值
```
function func(arg='test') {
  console.log(arg)
}

// 使用默认值
func()          // 'test'
func(undefined) // 'test'

// 禁用默认值
func(null)      // null
```

#### 参数还可以是函数调用表达式
参数的默认值可以是一个函数调用表达式
```
function getValue() {
    return 5;
}

function add(first, second = getValue()) {
    return first + second;
}
```

函数调用表达式的调用时机：第二个参数缺失并且调用`add()`方法时，会执行该函数调用表达式。（当`add()`函数编译时不会执行该函数调用表达式）
```
let value = 5;

function getValue() {
    return value++;
}

function add(first, second = getValue()) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 6
console.log(add(1));        // 7
```

#### 后面的参数可以访问前面的参数，反之则不能
```
function add(first, second = first) {
    return first + second;
}
console.log(add(1, 1));     // 2
console.log(add(1));        // 2

function add(first = second, second) {
    return first + second;
}
console.log(add(undefined, 1)); // throws error
```

【原因】：默认参数也有`TDZ`，当默认参数被初始化后才会从`TDZ`中移除，该参数才可以被访问。

## Unnamed Parameters
### in ES5
* 使用arguments来获取多余的参数
### in ES6

## 默认参数对arguments的影响
1. ES5非严格模式下，直接修改参数值会影响到arguments
```
function mixArgs(first) {
    console.log(first === arguments[0]);   // true
    first = "c";
    console.log(first === arguments[0]);   // true
}
```

2. ES5严格模式下，直接修改参数值不会影响到arguments
```
function mixArgs(first) {
    console.log(first === arguments[0]);   // true
    first = "c";
    console.log(first === arguments[0]);   // false
}
```

3. ES6中，直接修改参数值不会影响到arguments；并且设置了默认值的参数缺失时，将会使用默认参数，这时该参数和arguments没有关联
```
function mixArgs(first, second = "b") {
    console.log(arguments.length);         // 1
    console.log(first === arguments[0]);   // true
    console.log(second === arguments[1]);  // false
    first = "c";
    second = "d"
    console.log(first === arguments[0]);   // false
    console.log(second === arguments[1]);  // false
}
mixArgs("a")  // second缺失
```




1. 默认参数
2. rest参数
3. new Function
https://tc39.github.io/ecma262/#sec-ecmascript-function-objects
4. Arrow functions, discussed in the "Arrow Functions" section, do not have a [[Construct]] method
```
var a = () => {this.name = 12}
a()
var b = a()
b //undefined
var b = new a() // Uncaught TypeError: a is not a constructor

var a = function () {this.name = 12}
var b = new a()
b // {name: 12}
```

5. instanceof

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
