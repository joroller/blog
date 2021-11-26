# readonly 修饰符

## readonly 属性

TypeScript 提供了 readonly（只读） 修饰符，允许将类的属性标记为不可变。readonly 属性的赋值只能发生在以下两个地方之一

* 属性声明中
* 在同一个类的构造函数中

要将一个属性标记为不可变，可以使用 readonly 关键字。下面展示了如何在 Person 类中声明只读属性：

```typescript
class Person {
  readonly birthDate: Date;

  constructor(birthDate: Date) {
    this.birthDate = birthDate;
  }
}
```

在本例中，birthDate 属性是在 Person 类的构造函数中初始化的只读属性。

下面尝试重新给 birthDate 属性赋值，这会导致错误：

```typescript
let person = new Person(new Date(1990, 12, 25));
person.birthDate = new Date(1991, 12, 25); // Compile error
```

可以在构造函数中合并只读属性的声明和初始化：
```typescript
class Person {
  constructor(readonly birthDate: Date) {
    this.birthDate = birthDate;
  }
}
```
### Readonly vs. const

下面展示了 readonly 和 const 之间的区别::
	
|                   | readonly                                                        | const               |
| ----------------- | --------------------------------------------------------------- | ------------------- |
|  Use for          | Class properties                                                | Variables           |
| Initialization    | In the declaration or in the constructor of the same class      | In the declaration  |

### 一个极致的示例

```typescript
class Persion {
      
  private readonly name: {    // 声明属性
    readonly first: string,
    readonly last: string
  };

  constructor() {
    this.name = {             // 构造方法中初始化属性的值
      first: 'roller',
      last: 'wang'
    };
  }

  doSomething() {
    this.name = {};                 // Cannot assign to 'name' because it is a read-only property.
    this.name.first = 'Joroller';   // Cannot assign to 'first' because it is a read-only property.
    this.name.last = 'Wong';        // Cannot assign to 'last' because it is a read-only property.
  }
}
```

## readonly 数组类型

ReadonlyArray 是一种特殊类型，它描述了不能被修改的数组。

```typescript
function doStuff(values: ReadonlyArray<string>) {
  // We can read from 'values'...
  const copy = values.slice();
  console.log(`The first value is ${values[0]}`);
 
  // ...but we can't mutate 'values'.
  values.push("hello!"); // Property 'push' does not exist on type 'readonly string[]'.
}
```
当我们看到一个返回 ReadonlyArray 类型值的函数时，它提示使用者该结果不应该被修改。当我们看到一个函数使用 ReadonlyArray 类型的参数时，
它告诉我们可以将任何数组传递给该函数，而不必担心在方法体中它会的内容会被改变。

与 Array 不同，ReadonlyArray 没有构造函数可以使用。

例如：
```typescript
new ReadonlyArray("red", "green", "blue"); // 报错：'ReadonlyArray' only refers to a type, but is being used as a value here.
```

我们可以将常规数组赋值给 ReadonlyArrays 类型的变量，可以这样使用：
```typescript
const roArray: ReadonlyArray<string> = ["red", "green", "blue"];
```

就像 TypeScript 用 Type[] 为 Array<Type> 提供了一种简写语法一样，它也可以用 readonly Type[] 为 ReadonlyArray<Type> 提供了一种简写语法。

```typescript
function doStuff(values: readonly string[]) {
  // We can read from 'values'...
  const copy = values.slice();
  console.log(`The first value is ${values[0]}`);
 
  // ...but we can't mutate 'values'.
  values.push("hello!");  // Property 'push' does not exist on type 'readonly string[]'.
}
```

最后要注意的一点是，与 readonly 属性修饰符不同，常规数组和 ReadonlyArray 之间不能互相赋值。

```typescript
let x: readonly string[] = [];
let y: string[] = [];
 
x = y;
y = x; // 报错：The type 'readonly string[]' is 'readonly' and cannot be assigned to the mutable type 'string[]'.
```

## readonly Tuple（元组） 类型

### 什么是 Tuple（元组） 类型

Tuple（元组）类型是另一种数组类型，它确切地知道它包含多少元素，以及在特定位置包含哪些类型。

```typescript
type StringNumberPair = [string, number];
```
这里，StringNumberPair 是字符串和数字的元组类型。
和 ReadonlyArray 一样，它在运行时不会纯在，但对 TypeScript 的类型检查很重要。
对于类型系统，StringNumberPair 描述数组，其 0 索引包含一个字符串，1 索引包含一个数字。

```typescript
function doSomething(pair: [string, number]) {
  const a = pair[0];  // const a: string
  const b = pair[1];  // const b: number
  // ...
}
 
doSomething(["hello", 42]);
```

### readonly Tuple（元组） 类型

Tuple（元组）类型有 readonly 变体，并且可以通过在它们前面添加只 readonly 饰符来指定。

```typescript
function doSomething(pair: readonly [string, number]) {
  // ...
}
```

正如期望的那样，TypeScript 不允许向 readonly Tuple（元组）类型的变量做写入操作。

```typescript
function doSomething(pair: readonly [string, number]) {
  pair[0] = "hello!";  // 报错： Cannot assign to '0' because it is a read-only property.
}
```

在大多数代码中，Tuple（元组）倾向于创建并保持原样。因此，在可能的情况下将类型注释为 readonly 元组是一个很好的习惯。

另外，带有 const 断言(assertion) 的数组字面量将被认为是 readonly 元组类型。

```typescript
let point = [3, 4] as const;  // 被认为是 readonly 的元组类型
 
function distanceFromOrigin([x, y]: [number, number]) {
  return Math.sqrt(x ** 2 + y ** 2);
}
 
distanceFromOrigin(point);  // 报错：Argument of type 'readonly [3, 4]' is not assignable to parameter of type '[number, number]'.
                            // The type 'readonly [3, 4]' is 'readonly' and cannot be assigned to the mutable type '[number, number]'.
```


## 参考

[TypeScript readonly](https://www.typescripttutorial.net/typescript-tutorial/typescript-access-modifiers/)

[官方文档 - readonly Properties](https://www.typescriptlang.org/docs/handbook/2/objects.html#readonly-properties)

[官方文档 - The Readonly Array Type](https://www.typescriptlang.org/docs/handbook/2/objects.html#the-readonlyarray-type)

[官方文档 - readonly Tuple Types](https://www.typescriptlang.org/docs/handbook/2/objects.html#readonly-tuple-types)