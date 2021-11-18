# AngularJS 数据绑定符号（@ = < & ?）的使用

## 使用范围

* 在 Component 的 bindings 属性中使用
* 当 Directive 的 scope 属性值为 {} 对象哈希时，在 {} 中使用
  
## 符号的作用

在子级作用域中绑定父级作用域（scope）中的数据，可以实现单项绑定或则双向绑定。

## 单项绑定与双向绑定

### 单向绑定

单向绑定：修改子级的数据**不会**对父级的数据造成影响，但修改父级的数据**会**对子级的数据造成影响。

注意：

  * 单向绑定如果绑定的值是一个对象，那么在子级作用域中对该对象属性的更改将反映在父作用域中(因为两者引用相同的对象)。

### 双向绑定

双向绑定：子级作用域的数据与父级作用域完全**互通**，修改任意一级的数据**会**对另一级的数据造成影响。

## 符号 ?

可以使用在 @ = < & 符号后面（@? =? <? &?），代表实例属性会根据 DOM 属性的有无来决定是否被定义，具体见下文的举例。

## 符号 @

### 定义

@ 或者 @attr - 将本地作用域的实例属性（property）单向绑定到 DOM 属性（attribute）的值。因为 DOM 属性是字符串，所以绑定的结果总是一个字符串，即实例变量的类型始终为 String。如果没有指定 attr，则假设 DOM 的属性名与本地名称相同。

区别与 < 符号，@ 绑定的是一个字符串，而 < 绑定的是一个表达式。

### 举例

父级：myComtroller 的 $scope
```javascript
app.controller('myController',  ['$scope', '$timeout', function($scope, $timeout) {
  $scope.myFirstName = 'Joroller';
  $scope.myLastName = 'Wong';

  $timeout(() => {
    $scope.myFirstName = 'Roller'; // 1秒后重新赋值，这会影响子级作用域的firstName （3）
  }, 1000);

  $timeout(() => {
    console.log($scope.myFirstName) // Roller 不会获得子级作用域中修改后的值 （6）
  }, 3000);
}]);
```

```html
<body ng-controller="myController">
  <!-- 此处未添加 last-name 属性 -->
  <!-- 此处的 myFirstName 需要{{}}包裹 -->
  <my-info first-name="{{myFirstName}}"></my-info>
</body>
```

子级：myInfo 的 scope
```javascript
app.directive('myInfo', ['$timeout', function($timeout) {
  return {
    scope: {
      firstName: '@',
      lastName: '@?' // 因为父级的 DOM 标签中没有 last-name 属性，所以该实例变量 lastName 不会被声明
    },
    bindToController: true,
    controller: function() {
      this.$onInit = function() {
        console.log(this.firstName); // Joroller  （1）
        console.log(this.lastName); // undefined  （2）

        $timeout(() => {
          console.log(this.firstName); // Roller 获得父级作用域的值 （4）
          this.firstName = 'Another Name'; // 2秒后修改子级作用域的firstName，但这不会影响父级的myFirstName （5）
        }, 2000);
      }
    }
  }
}])
```

## 符号 =

### 定义
= 或者 =attr - 将本地作用域的实例属性（property）与 DOM 属性（attribute）的值（该值为一个表达式）进行双向绑定。如果没有指定 attr，则假设 DOM 的属性名与本地名称相同。如果绑定表达式是不可赋值的，或者属性不是可选的并且不存在，当发现本地作用域值的变化时，将抛出一个异常，因为不可能将它们同步回父级作用域。

### 举例

父级：myComtroller 的 $scope
```javascript
app.controller('myController',  ['$scope', '$timeout', function($scope, $timeout) {
  $scope.myFirstName = 'Joroller';
  $scope.myLastName = 'Wong';

  $timeout(() => {
    $scope.myFirstName = 'Roller'; // 1秒后重新赋值 （3）
  }, 1000);

  $timeout(() => {
    console.log($scope.myFirstName) // Another Name 获得子级作用域中修改后的值 （6）
  }, 3000);
}]);
```

```html
<body ng-controller="myController">
  <!-- 此处未添加 last-name 属性 -->
  <!-- 此处的 myFirstName 为一个变量（即表达式），不需要{{}}包裹 -->
  <my-info first-name="myFirstName"></my-info>
</body>
```

子级：myInfo 的 scope
```javascript
app.directive('myInfo', ['$timeout', function($timeout) {
  return {
    scope: {
      firstName: '=',
      lastName: '=?' // 因为父级的 DOM 标签中没有 last-name 属性，所以该实例变量 lastName 不会被声明
    },
    bindToController: true,
    controller: function() {
      this.$onInit = function() {
        console.log(this.firstName); // Joroller  （1）
        console.log(this.lastName); // undefined  （2）

        $timeout(() => {
          console.log(this.firstName); // Roller 获得父级作用域中修改值 （4）
          this.firstName = 'Another Name'; // 2秒后修改子级作用域的firstName，这会影响父级的myFirstName （5）
        }, 2000);
      }
    }
  }
}])
```

## 符号 <

### 定义

< 或者 <attr - 将本地作用域的实例属性（property）与 DOM 属性（attribute）的值（该值为一个表达式）进行单向绑定。如果没有指定 attr，则假设 DOM 的属性名与本地名称相同。

区别与 @ 符号，< 绑定的是一个表达式，而 @ 绑定只能是一个字符串。

### 举例

父级：myComtroller 的 $scope
```javascript
app.controller('myController',  ['$scope', '$timeout', function($scope, $timeout) {
  $scope.myFirstName = 'Joroller';
  $scope.myLastName = 'Wong';

  $timeout(() => {
    $scope.myFirstName = 'Roller'; // 1秒后重新赋值 （3）
  }, 1000);

  $timeout(() => {
    console.log($scope.myFirstName) // Roller 不能获得子级作用域中修改后的值 （6）
  }, 3000);
}]);
```

```html
<body ng-controller="myController">
  <!-- 此处未添加 last-name 属性 -->
  <!-- 此处的 myFirstName 为一个变量（即表达式），不需要{{}}包裹 -->
  <my-info first-name="myFirstName"></my-info>
</body>
```html

```

子级：myInfo 的 scope
```javascript
app.directive('myInfo', ['$timeout', function($timeout) {
  return {
    scope: {
      firstName: '<',
      lastName: '<?' // 因为父级的 DOM 标签中没有 last-name 属性，所以该实例变量 lastName 不会被声明
    },
    bindToController: true,
    controller: function() {
      this.$onInit = function() {
        console.log(this.firstName); // Joroller  （1）
        console.log(this.lastName); // undefined  （2）

        $timeout(() => {
          console.log(this.firstName); // Roller 获得父级作用域中修改值 （4）
          this.firstName = 'Another Name'; // 2秒后修改子级作用域的firstName，但不会影响父级的myFirstName （5）
        }, 2000);
      }
    }
  }
}])
```

## 符号 &

### 定义

& 或者 &attr - 提供在父级作用域上下文中执行表达式的方法。

通常希望通过表达式将数据从子级作用域传递到父级作用域，可以通过向表达式中的包装器方法传递由子级变量的 name: value 组成的 map 参数来实现。

### 举例

父级：myComtroller 的 $scope
```javascript
app.controller('myController',  ['$scope', '$timeout', function($scope, $timeout) {
  $scope.myName = 'Joroller Wong';

  $scope.changeName = (anotherName) => {
    $scope.myName = anotherName; // 接收到子级的数据 'Roller Wong'  （2）
    console.log($scope.myName); // 'Roller Wong'
  }
}]);
```

```html
<body ng-controller="myController">
  <!-- 与父级作用域的 changeName() 方法进行绑定-->
  <!-- 此处的 changeName(newName) 为方法的调用（即表达式），不需要{{}}包裹 -->
  <!-- changeName(newName) 表达式将被包装到一个方法中，并绑定给子作用域的 changeName 属性（property） -->
  <!-- 当在子作用域执行 changeName() 时，才会执行该表达式-->
  <my-info change-name="changeName(newName)" my-name="myName"></my-info>
</body>
```

子级：myInfo 的 scope
```javascript
app.directive('myInfo', function() {
  return {
    scope: {
      changeName: '&'   // 将表达式包装成方法，并被 changeName 属性（property）指向，即调用 changeName() 时会执行表达式
    },
    bindToController: true,
    controller: function() {
      this.$onInit = function() {
        // 需要通过 map 的形式进行数据的传递
        // 这里的 newName 需要与 Dom 属性 change-name="changeName(newName)" 中的 newName 一致
        this.changeName({newName: 'Roller Wong'});  // 将子作用域的数据 'Roller Wong' 传递给父级作用域  （1）
      }
    }
  }
})
```

## 参考

[官方文档 - scope](https://docs.angularjs.org/api/ng/service/$compile#-scope-)

[官方文档 - bindings](https://docs.angularjs.org/guide/component#component-based-application-architecture)
