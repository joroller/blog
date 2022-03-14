# 反转

## 1. 整数反转

思想：利用 % 与 / 操作符。其中操作符的右侧操作数是 10。 

### 1.1 最优解

```java
public static int numberReverse(int number) {
int result = 0;

while (number != 0) {
    // 求取个位数
    int lastDigit = number % 10;
    // 缩小 10 倍
    number /= 10;

    // 将上一次得到的结果放大 10 倍，并且加上当前的个位数
    result = result * 10 + lastDigit;
}

return result;
}
```

### 1.2 借助数据结构

这里使用 LinkedList。

```java
public static int numberReverse2(int number) {
  LinkedList<Integer> digits =  new LinkedList<>();
  int multiple = 1;
  while (number != 0) {
    // 求取个位数
    digits.add(number % 10);
    // 计算倍数 - 每循环一次放大 10 倍
    multiple *= 10;
  }

  int result = 0;
  // 从头开始遍历
  for (Integer digit : digits) {
    result += digit * multiple;
    // 计算倍数 - 每循环一次缩小 10 倍
    multiple /= 10;
  }

  return result;
}
```

### 复杂度分析
