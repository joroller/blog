# 排序（Sort）

## 1. 冒泡排序（Bubble Sort）

### 1.1 基本思想

从序列头部开始遍历：

* 升序 - 两个数比较大小，较小的数下沉，较大的数冒起来
* 降序 - 两个数比较大小，较大的数下沉，较小的数冒起来

从序列尾部开始遍历：

* 升序 - 两个数比较大小，较大的数下沉，较小的数冒起来
* 降序 - 两个数比较大小，较小的数下沉，较大的数冒起来

### 1.2 动作拆分

* 确定起始位置，序列的头部还是尾部开始
* 依次取两个数
* 前后两个数比较
* 前后两个数交换位置

### 1.3 伪代码

```
for(外部循环控制内循环的终止位置) { // 外层循环（序列长度 - 1）次
  for(内部循环用于取相邻两数) {   // 内层循环可以控制
    if(前面的数 < 或 > 后面的数) {      // 对比的 < 或 > 会影响到排序的升降结果
      数位置交换;
    }
  }
}
```

### 1.3 代码

```java
/*
  *  从序列头部开始遍历：升序 - 两个数比较大小，较小的数下沉，较大的数冒起来
  */
  public static int[] bubbleSortWithAscendingOrder1(int[] numbers) {
    int temp;
    for(int i = numbers.length - 1; i > 0; i--) {
      for(int j = 0; j < i ; j++) {
        if(numbers[j] > numbers[j + 1]) {   // 两数比较，大的往尾部移动
          temp = numbers[j];
          numbers[j] = numbers[j + 1];
          numbers[j + 1] = temp;
        }
      }
    }

    return numbers;
  }

  /*
  * 从序列尾部开始遍历：升序 - 两个数比较大小，较大的数下沉，较小的数冒起来
  */
  public static int[] bubbleSortWithAscendingOrder2(int[] numbers) {
    int temp;
    for(int i = 0; i < numbers.length - 1; i++) {
      for(int j = numbers.length - 1; j > i; j--) {
        if(numbers[j - 1] > numbers[j]) {   // 两数比较，小的往头部移动
          temp = numbers[j - 1];
          numbers[j - 1] = numbers[j];
          numbers[j] = temp;
        }
      }
    }

    return numbers;
  }

  /*
   * 从序列头部开始遍历：降序 - 两个数比较大小，较大的数下沉，较小的数冒起来
   */
  public static int[] bubbleSortWithDescendingOrder1(int[] numbers) {
    int temp;
    for(int i = numbers.length - 1; i > 0; i--) {
      for(int j = 0; j < i; j++) {
        if(numbers[j] < numbers[j + 1]) {   // 两数比较，小的往尾部移动
          temp = numbers[j];
          numbers[j] = numbers[j + 1];
          numbers[j + 1] = temp;
        }
      }
    }

    return numbers;
  }

  /*
   * 从序列尾部开始遍历：降序 - 两个数比较大小，较小的数下沉，较大的数冒起来
   */
  public static int[] bubbleSortWithDescendingOrder2(int[] numbers) {
    int temp;
    for(int i = 0; i < numbers.length - 1; i++) {
      for(int j = numbers.length - 1; j > i; j--) {
        if(numbers[j - 1] < numbers[j]) {   // 两数比较，大的往头部移动
          temp = numbers[j - 1];
          numbers[j - 1] = numbers[j];
          numbers[j] = temp;
        }
      }
    }

    return numbers;
  }
```

## 2. 选择排序（Selction Sort）