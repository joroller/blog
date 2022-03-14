# 斐波那契数列

## 1. 介绍

[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233，377，610，987，1597，2584，4181，6765，10946，17711，28657，46368……]

**分析：**
    * 一般情况：这个数列从第三项开始，每一项都等于前两项之和
    * 特殊情况：第 1 项是 0，第 2 项是 1

**公式：**

    f(n) = f(n -1) + f(n -2);

    条件：n >= 3

    f(1) = 0;
    f(2) = 1;

## 2. 代码实现

### 2.1 通过递归实现

**分析：**

用公式 `f(n) = f(n -1) + f(n -2);` 很容易想到使用递归。

**步骤1：解决一般情况**

```java
long fibonacci(long n) {
  if(n >= 3) {  // 从第 3 项开始才算一般情况
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
  return -1L;
}
```

**步骤2：处理特殊情况**

```java
long fibonacci(long n) {
  if(n == 1) {  // 特殊处理第 1 项
    return 0; // 返回值比 n 小 1
  }

  if(n == 2) {  // 特殊处理第 2 项
    return 1; // 返回值比 n 小 1
  }

  if(n >= 3) {
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
  return -1L;
}
```

**步骤3：优化步骤2**

因为当 n = 1 和 2 时，返回的值始终比 n 小 1，那么可以如下优化：

```java
long fibonacci(long n) {
  if(n == 1 || n == 2) {  // 统一特殊处理第 1 项和第 2 项
    return n - 1;
  }

  if(n >= 3) { 
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
  return -1L;
}
```
### 2.2 通过循环实现

**分析：**

通过值的交换来达到目的。步骤：
1. [3, 5]，假设 firstNum = firstNum = 5，将 5 的下一个值标记为 result
2. 通过公式 `f(n) = f(n -1) + f(n -2);` 可以将其带入得到 `result = secondNum + firstNum`，该例中 result 为 5 + 3 = 8
3. 如果需要得到 8 后面的值，那么需要将 first 的值修改为 second 的值，将 second 的值修改为 result 的值，在本例中 first = 5， second = 8
4. 重复步骤 2 与 3 得到 8 后面的 result 值为 8 + 5 = 13
5. ...

**步骤1：解决一般情况**

```java
long fibonacci(long n) {
  if(n >= 3) {  // 从第 3 项开始才算一般情况
    long nextNum = 1, secondNum = 1, firstNum = 0;
    for(int i = 0; i < n; i++) {
      nextNum = secondNum + firstNum; // 得出下一个值
      firstNum = secondNum; // 更新值，准备计算下下个值
      secondNum = nextNum; // 更新值，准备计算下下个值
    }
    return nextNum;
  }
  
  return -1L;
}
```

**步骤2：处理特殊情况**

```java
public static long fibonacci(long n) {
  if(n == 1 || n == 2) {  // 统一特殊处理第 1 项和第 2 项
    return n - 1;
  }

  if(n >= 3) {
    long nextNum = 1, secondNum = 1, firstNum = 0;
    for(int i = 0; i < n; i++) {
      nextNum = secondNum + firstNum;
      firstNum = secondNum;
      secondNum = nextNum;
    }
    return nextNum;
  }

  return -1L;
}
```

**步骤3：优化步骤2**

```java
long fibonacci(long n) {
  if(n < 1) {
    return -1L;
  }

  if(n == 1 || n == 2) {
    return n - 1;
  }

  long nextNum = 1, secondNum = 1, firstNum = 0;
  for(int i = 0; i < n - 2; i++) { // 当 n >= 3 时才进入循环
    nextNum = secondNum + firstNum;
    firstNum = secondNum;
    secondNum = nextNum;
  }
  return nextNum;
}
```

### 2.3 通过循环与数组实现

刚方法与通过循环实现类似，但该方法通过数组可以保留整个序列。

```java
long fibonacci(int n) {
  if(n < 1) {
    return -1L;
  }

  if(n == 1 || n == 2) {
    return n;
  }

  long[] sequence = new long[n];
  sequence[0] = 0;  // 特殊处理第 1 项
  sequence[1] = 1;  // 特殊处理第 2 项

  for(int i = 2; i < n; i++) { // 当 n >= 3 时才进入循环
    sequence[i] = sequence[i - 2] + sequence[i - 1];
  }
  return sequence[n -1];
}
```

### 2.4 通过缓存优化递归

```java
public class FibonacciWithCirculationAndCache {

  public static long[] cache;

  public static long fibonacci(int n) {
    if(n < 1) {
      return -1L;
    }

    if ((n == 1) || (n == 2)) {
      return n - 1;
    }

    if(cache != null && cache[n - 1] != -1) {
      return cache[n - 1];
    }

    if(n >= 3) {
      if(cache == null) {
        cache = new long[n];
        Arrays.fill(cache, -1);
      }
      cache[0] = 0;
      cache[1] = 1;
      cache[n-1] = (fibonacci(n - 1) + fibonacci(n - 2));
    }
    return cache[n - 1];
  }
}
```

## 复杂度分析

// TODO





