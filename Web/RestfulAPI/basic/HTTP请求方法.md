# HTTP请求方法

## POST


## DELETE


## PUT


## PATCH

在HTTP协议中，请求方法 PATCH 用于对资源进行部分修改。

对资源修改的请求方法还有PUT，但PUT要求前端提供一个完整的资源对象，理论上说，如果你用了PUT，但却没有提供完整的资源对象，那么缺了的那些字段应该被清空。

**举例**： 商品添加购物车，如果第一次添加1个，发送一次请求后数据库会存储数量为1，第二次再添加2个并发送请求，此时数据库应该存储数量为3，而不是重写之前的1为2。

**参考**：[PATCH](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PATCH)

## GET

## 术语

### 幂等

在编程中一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。幂等函数，或幂等方法，是指可以使用相同参数重复执行，并能获得相同结果的函数。这些函数不会影响系统状态，也不用担心重复执行会对系统造成改变。例如，“setTrue()”函数就是一个幂等函数,无论多次执行，其结果都是一样的.更复杂的操作幂等保证是利用唯一交易号(流水号)实现。


## 参考资料

[HTTP请求方法](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods)

