# JavaScript 变量声明中的 var，let 和 const

## 变量声明

变量声明有三种方式 var, let 和 const。

let 和 const 是 JavaScript 里相对较新的变量声明方式。let 在很多方面与 var 是相似的，但是可以帮助大家避免在 JavaScript 里常见一些问题。
const 是对 let 的一个增强，它能阻止对一个变量再次赋值。

## var 声明

用 var 声明的变量的作用域是它当前的执行上下文，它可以是嵌套的函数，或者对于声明在任何函数外的变量来说是全局。
如果你重新声明一个 JavaScript 变量，它将不会丢失其值。

当赋值给未声明的变量(未使用var 声明，直接进行赋值操作), 则执行赋值后, 该变量会被隐式地创建为全局变量（它将成为全局对象的属性）。

举例：
```javascript
function x() {
  y = 1;   // 在严格模式（strict mode）下会抛出 ReferenceError 异常
  var z = 2;
}

x();

console.log(y); // 打印 "1"
console.log(z); // 抛出 ReferenceError: z 未在 x 外部声明
```

多次声明一个变量时，只会得到 1 个。

举例：
```javascript
function f(x) {
  var x;
  var x;

  if (true) {
    var x;
  }
}
```
在上面的例子里，所有 x 的声明实际上都引用一个相同的 x，并且这是完全有效的代码。 这经常会成为 bug 的来源。 可以使用 let 声明来解决这个问题。

### 作用域规则

对于熟悉其它语言的人来说，var 声明有些奇怪的作用域规则。看下面的例子：
```javascript
function f(shouldInitialize: boolean) {
  if (shouldInitialize) {
    var x = 10;
  }

  return x;
}

f(true);  // returns '10'
f(false); // returns 'undefined'
```
变量 x 是定义在 if 语句里面，但是我们却可以在语句的外面访问它。 
这是因为 var 声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问（我们后面会详细介绍），包含它的代码块对此没有什么影响。 
有些人称此为**var作用域或函数作用域**。 函数参数也使用函数作用域。

这些作用域规则可能会引发一些错误。 其中之一就是，多次声明同一个变量并不会报错：
```javascript
function sumMatrix(matrix: number[][]) {
    var sum = 0;
    for (var i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (var i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }

    return sum;
}
```

这里很容易看出一些问题，里层的 for 循环会覆盖变量 i，因为所有 i 都引用相同的函数作用域内的变量。这些问题可能在代码审查时漏掉，引发无穷的麻烦。

### 变量提升

由于变量声明（以及其他声明）总是在任意代码执行之前处理的，所以在代码中的任意位置声明变量总是等效于在代码开头声明。
这意味着变量可以在声明之前使用，这个行为叫做**hoisting**。hoisting 就像是把所有的变量声明移动到函数或者全局代码的开头位置。

```javascript
bla = 2
var bla;
// ...

// 可以隐式地（implicitly）将以上代码理解为：

var bla;
bla = 2;
```

### 将 var 变量用于 setTimeout() 方法中

```javascript
for (var i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
```
setTimeout 会在若干毫秒的延时后执行一个函数。

期望返回结果：
```shell
0
1
2
3
4
5
6
7
8
9
```
实际返回结果：
```shell
10
10
10
10
10
10
10
10
10
10
```

原因：

我们传给 setTimeout 的每一个函数表达式实际上都引用了相同作用域里的同一个 i。

setTimeout 在若干毫秒后执行一个函数，但执行这个函数时主线程的 for 循环已经结束。 for 循环结束后，i 的值为 10。 所以当函数被调用的时候，它会打印出 10！

解决办法：

一个通常的解决方法是使用立即执行的函数表达式（IIFE）来捕获每次迭代时 i 的值：

```javascript
for (var i = 0; i < 10; i++) {
  // capture the current state of 'i'
  // by invoking a function with its current value
  (function(i) {
      setTimeout(function() { console.log(i); }, 100 * i);
  })(i);
}
```
参数 i 会覆盖 for 循环里的 i，但是因为我们起了同样的名字，所以我们不用怎么改 for 循环体里的代码。

## let 声明

现在我们已经知道了 var 存在一些问题，这恰好说明了为什么用 let 语句来声明变量。除了名字不同外，let 与 var 的写法一致。
主要的区别不在语法上，而是语义，我们接下来会深入研究。

### 块作用域

当用 let 声明一个变量，它使用的是词法作用域或块作用域。 
不同于使用 var 声明的变量那样可以在包含它们的函数外访问，块作用域变量在包含它们的块或 for 循环之外是不能访问的。

```javascript
function f(input: boolean) {
  let a = 100;

  if (input) {
    // Still okay to reference 'a'
    let b = a + 1;
    return b;
  }

  // Error: 'b' doesn't exist here
  return b;
}
```

这里我们定义了2个变量 a 和 b。 a 的作用域是 f 函数体内，而b的作用域是 if 语句块里。
在 catch 语句里声明的变量也具有同样的作用域规则。

```javascript
try {
  throw "oh no!";
}
catch (e) {
  console.log("Oh well.");
}

// Error: 'e' doesn't exist here
console.log(e);
```

拥有块级作用域的变量的另一个特点是，它们不能在被声明之前读或写。
虽然这些变量始终“存在”于它们的作用域里，但在直到声明它的代码之前的区域都属于**[暂时性死区](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let#%E6%9A%82%E5%AD%98%E6%AD%BB%E5%8C%BA)**。 它只是用来说明我们不能在 let 语句之前访问它们。

```javascript
a++; // ReferenceError: a is not defined
let a;
```

### 重定义及屏蔽

上文提过使用 var 声明时，它不在乎你声明多少次；你只会得到 1 个。
但 let 声明没有此情况。

```javascript
let x = 10;
let x = 20; // SyntaxError: Identifier 'x' has already been declared
```

并不是说块级作用域变量不能用函数作用域变量来声明。而是块级作用域变量需要在明显不同的块里声明。

```javascript
function f(condition, x) {
  if (condition) {
    let x = 100;
    return x;
  }

  return x;
}

f(false, 0); // returns 0
f(true, 0);  // returns 100
```

在一个嵌套作用域里引入一个新名字的行为称做**屏蔽**。 它是一把双刃剑，它可能会不小心地引入新问题，同时也可能会解决一些错误。
例如，假设我们现在用 let 重写之前的 sumMatrix 函数。

```javascript
function sumMatrix(matrix: number[][]) {
  let sum = 0;
  for (let i = 0; i < matrix.length; i++) {
    var currentRow = matrix[i];
    for (let i = 0; i < currentRow.length; i++) {
      sum += currentRow[i];
    }
  }

  return sum;
}
```

这个版本的循环能得到正确的结果，因为内层循环的 i 可以屏蔽掉外层循环的 i。

通常来讲应该避免使用屏蔽，因为我们需要写出清晰的代码。同时也有些场景适合利用它，需要好好算计一下。

### 块级作用域变量的获取

直观地讲，每次进入一个作用域时，它创建了一个变量的环境。 就算作用域内代码已经执行完毕，这个环境与其捕获的变量依然存在。

```javascript
function theCityThatAlwaysSleeps() {
  let getCity;

  if (true) {
    let city = "Seattle";
    getCity = function() {
      return city;
    }
  }

  return getCity();
}
```
因为我们已经在 city 的环境里获取到了 city，所以就算 if 语句执行结束后我们仍然可以访问它。

### 将 let 变量用于 setTimeout() 方法中

```javascript
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() {console.log(i); }, 100 * i);
}
```

期望与实际结果：
```shell
0
1
2
3
4
5
6
7
8
9
```

当 let 声明出现在循环体里时不仅在循环里引入了一个新的变量环境，而且针对每次迭代都会创建一个新作用域。

## const 声明

const 声明是声明变量的另一种方式。
它们与 let 声明相似，但是就像它的名字所表达的，它们被赋值后不能再改变。换句话说，它们拥有与 let 相同的作用域规则，但是不能对它们重新赋值。
const 声明的声明变量需要有一个初始值。

除非你使用特殊的方法去避免，实际上 const 变量的内部状态是可修改的。

关于**[暂时性死区](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let#%E6%9A%82%E5%AD%98%E6%AD%BB%E5%8C%BA)**的所有讨论都适用于let和const。

## let vs. const

我们有两种作用域相似的声明方式，那自然会问到底应该使用哪个。答案是：依情况而定。

使用最小特权原则，所有变量除了你计划去修改的都应该使用const。
基本原则就是如果一个变量不需要对它写入，那么其它使用这些代码的人也不能够写入它们，并且要思考为什么会需要对这些变量重新赋值。
使用 const 也可以让我们更容易的推测数据的流动。

## 参考

[TypeScript - 变量声明](https://www.tslang.cn/docs/handbook/variable-declarations.html)

[MDN - var](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var)

[MDN - let](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let)

[MDN - const](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/const)
