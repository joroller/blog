# Promise 异步支持

## 定义

Promise 是给**尚未得到的结果**做一个替身，这个替身就是 Promise 的实例对象。
通过 Promise 实例暴露出的 then() 方法可以预先定义得到结果后的行为，或者在获得结果的过程中发生错误后的行为。
当结果得到或发生错误后会自动执行（回调）这些行为。

## 使用范围

* 请求 API
* 可能存在阻塞的代码
  
## Promise 的三种状态

Promise 是一个有状态的对象，可能处于如下 3 种状态之一：

* 待定（ pending）
* 兑现（ fulfilled，有时候也称为“解决”， resolved）
* 拒绝（ rejected）

**待定**（ pending）是 Promise 对象的最初始状态。在待定状态下，当 Promise 对象成功获得结果后转换为**兑现**（ fulfilled）状态，
或者获得结果过程做发生错误（不能获得结果）则变为**拒绝**（ rejected）状态。状态的转变时不可逆的，
同时这些状态时不能通过外部方法来修改或则获取到的。要想改变状态，只能通过调用**执行函数**提供的特定函数参数，详见后文。

## 执行函数

由于 Promise 对象的状态是私有的，所以只能在内部进行操作。内部操作在Promise 对象的执行器函数中完成。
执行器函数主要有两项职责：初始化 Promise 对象的异步行为和控制状态的最终转换。其中，控制Promise 对象状态的转换是
通过调用它的两个函数参数实现的。这两个函数参数通常都命名为 resolve() 和 reject()。
调用 resolve() 会把状态切换为兑现，调用 reject() 会把状态切换为拒绝。

执行函数可以简单的理解为创建 Promise 对象时，为构造方法所提供的参数，只是这个参数是一个方法。这个方法中默认有两个参数，
通常可以命名为 resolve 和 reject，这两个参数也是方法。

**举例**：
```javascript
let p = new Promise((resolve, reject) => {

})

```
其中以下部分是执行函数
```javascript
(resolve, reject) => {

}
```
以下部分为执行函数中默认提供的两个方法参数
```javascript
(resolve, reject)
```

**状态转换：待定 -> 兑现**
```javascript
let p = new Promise((resolve, reject) => {
  resolve(); // 调用 resolve() 后会自动转换为兑现状态。实际开发中，一般会在期望的行为发生后再调用 resolve()。
})
```

**状态转换：待定 -> 拒绝**
```javascript
let p = new Promise((resolve, reject) => {
  reject(); // 调用 reject() 后会自动转换为拒绝状态。实际开发中，一般会在期望的行为没有发生并且无能为力的时候再调用 reject()。
})
```

## 实例方法

### .then()

.then() 可以为 Promise 实例预先添加处理程序。这个 then() 方法接收最多两个参数： 
onResolved 处理程序和 onRejected 处理程序。这两个参数都是可选的，如果提供的话，
则会在 Promise 实例分别进入“兑现”和“拒绝”状态时执行预定义好的处理程序。

在 Promise 实例上调用 .then() 方法后，会返回一个新的 Promise 实例。

**举例**：

onResolved() 处理程序被执行
```javascript
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(); // 3 秒后调用 resolve() 方法
  }, 3000);
});

let onResolved = () => {console.log('resolved')};  // 3 秒后显示 resolved
let onRejected = () => {console.log('rejected')};  // 这里的代码不会执行

p.then(onResolved, onRejected);
```

reject() 处理程序被执行
```javascript
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(); // 3 秒后调用 reject() 方法
  }, 3000);
});

let onResolved = () => {console.log('resolved')};  // 这里的代码不会执行
let onRejected = () => {console.log('rejected')};  //  3 秒后显示 rejected

p.then(onResolved, onRejected);
```

**注意**：
* 因为两个处理程序参数都是可选的。如果想只提供 onRejected 参数，那就要在 onResolved 参数的位置上传入 null。
* 传给 then() 的任何非函数类型的参数都会被静默忽略。

### .catch()
.catch() 方法用于给 Promise 对象添加拒绝处理程序。这个方法只接收一个参数：
onRejected 处理程序。事实上，这个方法就是一个语法糖，调用它就相当于调用 .then(null, onRejected)。

在 Promise 实例上调用 .catch() 方法后，会返回一个新的 Promise 实例。

**举例**：

```javascript
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(); // 3 秒后调用 reject() 方法
  }, 3000);
});

let onRejected = () => {console.log('rejected')};  //  3 秒后显示 rejected

p.catch(onRejected);
```

### .then() 与 .catch() 方法一起使用

当同时使用 .then() 与 .catch() 方法在一个 Promise 实例上，一般将在 .then() 方法中传入 onResolved 参数，
在 .catch() 方法中传入 onRejected 参数，并且将 .catch() 方法放在最后。

**举例**：

```javascript
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(); // 3 秒后调用 reject() 方法
  }, 3000);
});

let onResolved = () => {console.log('resolved')};  // 这里的代码不会执行
let onRejected = () => {console.log('rejected')};  //  3 秒后显示 rejected

p.then(onResolved).catch(onRejected);
```

### .finally()

.finally() 方法用于给 Promise 实例添加 onFinally 处理程序，这个处理程序在 Promise 实例转换为解决或拒绝状态时都会执行。
这个方法可以避免 onResolved 和 onRejected 处理程序中出现冗余代码。但 onFinally 处理程序没有办法知道 Promise 实例对象的状态是解决还是拒绝，
所以这个方法主要用于添加清理代码。

**举例**：

```javascript
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(); // 3 秒后调用 reject() 方法
  }, 3000);
});

let onRejected = () => {console.log('rejected')};  //  3 秒后显示 rejected
let onFinally = () => {console.log('finally')};  //  3 秒后显示 finally

p.catch(onRejected).finally(onFinally);
```

## 静态方法

### Promise.resolve()

Promise 实例并非一开始就必须处于待定状态，然后通过执行器函数才能转换为落定状态。
通过调用 Promise.resolve() 静态方法，可以实例化一个处于 **兑现** 状态的 Promise。

**举例**：

```javascript
let p = Promise.resolve();

let onResolved = () => {console.log('resolved')};  // resolved
let onRejected = () => {console.log('rejected')};  // 这里的代码不会执行

p.then(onResolved, onRejected);
```

### Promise.reject()

通过调用 Promise.reject() 静态方法，可以实例化一个处于 **拒绝** 状态的 Promise。

**举例**：

```javascript
let p = Promise.reject();

let onResolved = () => {console.log('resolved')};  // 这里的代码不会执行
let onRejected = () => {console.log('rejected')};  // rejected

p.then(onResolved, onRejected);
```

## Promise 链

多个 Promis 组合在一起可以构成强大的代码逻辑。这种组合可以通过两种方式实现：Promis 链与 Promis合成。
前者就是一个 Promis 接一个 Promis 地拼接，后者则是将多个 Promis 组合为一个 Promise。

把 Promis 逐个地串联起来是一种非常有用的编程模式。之所以可以这样做，是因为每个 Promise 实例的方
法（ then()、 catch()和 finally()）都会返回一个新的 Promise 对象，而这个新 Promise 又有自己的实例方法。
这样连缀方法调用就可以构成所谓的“Promise 链”。

要实现 Promise 链，则需要将**每个执行器都返回（return）一个 Promise 实例**。这样就可以让每
个后续 Promise 都等待之前的 Promise，也就是**串行化异步任务**。

**举例**：
```javascript
let p1 = new Promise((resolve, reject) => {   // 第1个 Promise
  console.log('p1 executor');
  setTimeout(() => {
    resolve();  // 异步操作，1秒后将状态改为兑现
  }, 1000);
});

p1.then(() => {
  return new Promise((resolve, reject) => {   // 第2个 Promise
    console.log('p2 executor');   // 只有第1个 Promise 的状态从待定转变为兑现后才会执行此代码
    setTimeout(() => {
      resolve();  // 异步操作，1秒后将状态改为兑现
    }, 1000);
  })
})

.then(() => {
  return new Promise((resolve, reject) => {   // 第3个 Promise
    console.log('p3 executor');   // 只有第2个 Promise 的状态从待定转变为兑现后才会执行此代码
    setTimeout(() => {
      resolve();  // 异步操作，1秒后将状态改为兑现
    }, 1000);
  })
})

.then(() => {
  return new Promise((resolve, reject) => {   // 第4个 Promise
    console.log('p4 executor');   // 只有第3个 Promise 的状态从待定转变为兑现后才会执行此代码
    setTimeout(() => {
      resolve();  // 异步操作，1秒后将状态改为兑现
    }, 1000);
  })
});
```
运行结果为：
```
p1 executor
p2 executor
p3 executor
p4 executor
```
每个后续的处理程序都会等待前一个 Promise 解决，然后实例化一个新 Promise 并返回它。这种结构可以简洁地将异步任务串行化。

## Promise 合成

Promise 合成会将多个 Promis 组合为一个 Promise.

Promise 类提供两个将多个 Promise 实例组合成一个 Promise 的静态方法： Promise.all() 和 Promise.race()。
而合成后 Promise 的行为取决于内部 Promise 的行为。

### Promise.all()

Promise.all() 静态方法创建的 Promise 会在一组 Promise 全部解决之后再解决。这个静态方法接收一个
可迭代对象，返回一个新 Promise：

```javascript
let p = Promise.all([
  Promise.resolve(),
  Promise.resolve()
]);
```
**注意**：

* 合成的 Promise 只会在每个包含的 Promise 都解决之后才解决。
* 如果至少有一个包含的 Promise 待定，则合成的 Promise 也会待定。如果有一个包含的 Promise 拒绝，则合成的 Promise 也会拒绝。
* 如果所有 Promise 都成功解决，则合成 Promise 的解决值就是所有包含 Promise 解决值的数组。
* 如果有 Promise 拒绝，则第一个拒绝的 Promise 会将自己的理由作为合成 Promise 的拒绝理由。之后再拒绝的 Promise 不会影响最终 Promise 的拒绝理由。

### Promise.race()

Promise.race()静态方法返回一个包装 Promise ，是一组集合中最先解决或拒绝的 Promise 的镜像。这个
方法接收一个可迭代对象，返回一个新 Promise ：

```javascript
let p = Promise.all([
  Promise.resolve(),
  Promise.resolve()
]);
```

**注意**：

* Promise.race() 不会对解决或拒绝的 Promise 区别对待。无论是解决还是拒绝，只要是第一个落定的 Promise ，Promise.race()就会包装其解决值或拒绝理由并返回新 Promise 。
* 如果有一个 Promise 拒绝，只要它是第一个落定的，就会成为拒绝合成 Promise 的理由。之后再拒绝的 Promise 不会影响最终 Promise 的拒绝理由。不过，这并不影响所有包含 Promise 正常的拒绝操作。

## 参考

[MDN - Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
[JavaScript高级程序设计(第4版)](https://www.ituring.com.cn/book/2472)

