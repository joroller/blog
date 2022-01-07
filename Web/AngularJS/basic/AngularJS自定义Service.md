# AngularJS 自定义 Service(服务)

## 什么是 Service（服务）

AngularJS 中的服务是通过依赖注入(DI)连接在一起的可替换对象。您可以通过应用程序中使用服务来组织和共享代码。

AngularJS 的服务有以下特点:

* **惰性实例化** - AngularJS 只在应用组件依赖某个服务时才实例化它。
* **单例** - 依赖于服务的每个组件都会得到单例的服务引用实例，这个实例由该服务的工厂来生成。换句话说，服务一但被注册到程序中，那么所有组件使用的服务都是同一个实例。

AngularJS 提供了一些有用的服务(比如 $http)，但对于大多数应用来说，创建自己的服务仍然是很必要的。

**注意**：

和 AngularJS 的其他核心标识符一样，内置服务总是以 `$` 开头(例如 \$http、\$provide)。

### 服务工厂

**服务工厂**（service factory）是一个函数，而该函数又是由**服务提供者**（service provider）创建。

服务提供者（service provider）实际上是一个构造函数。当它们被初始化时，他们必须包含一个叫做 $get 的属性，
该 $get 属性的值需要是一个函数，而这个函数就是服务工厂（service factory）函数。

**举例**:
// todo


当请求 Service（服务）时会经历两个步骤：

* \$injector 负责找到正确的服务提供者并实例化它。
* 然后调用服务提供者实例的 $get 服务工厂函数来获取该服务的实例。

Service（服务）通常没有配置选项，也不需要向服务提供者添加方法。服务提供者将仅仅只是一个带有 $get 属性的构造函数。

内置的 $provide 服务提供有辅助助器方法来注册自定义的服务，而无需指定提供者。下面来看是如何使用这些辅助助器方法来自定义一个 Service（服务）吧。

## 如何自定义一个 Service（服务）

### Service 的选择

AngularJS 中的 $provider 服务有很多方法用来向 $injector 注入器注册组件。这些方法也在 angular.Module 中提供，即有两种方法可以用来将自定义的 Service（服务）注册到程序中。

用户可以通过 $provider 或 angular.Module 中的 provider， factory， service， value， constant 和 decorator 来方法自定义一个Service（服务）。

* provider(name, provider) - 用 $injector 注册一个服务提供者
* constant(name, obj) - 注册一个可以被服务和服务提供者访问的值或者对象
* value(name, obj) - 注册一个只能由服务而不是服务提供者访问的值或者对象
* factory(name, fn) - 注册一个服务工厂函数，该工厂函数将被包装在服务提供者对象中，并且工厂函数会被自动赋值给服务提供者的 $get 属性
* service(name, Fn) - 注册一个构造函数，该构造函数将被包装在一个服务提供者对象中，并且服务提供者的 $get 属性的值会指向的该构造函数初始化的实例对象
* decorator(name, decorFn) - 注册一个 decorator 函数，该函数将能够修改或替换另一个服务的实现

有关更多信息和示例，请参阅各个方法。

### provider

通过 `$injector` 注册一个 provider 函数， Provider 函数是构造函数，其实例负责为服务提供一个工厂（$get 属性）。

服务提供者名称以其提供的服务名称开头，后面跟着 provider 。例如，$log 服务有一个名为 $logProvider 的提供程序。

服务提供者对象可以有额外的方法，这些方法允许对提供者及其服务进行配置。

重要的是，您可以配置由 `$get` 方法创建的服务类型，或者该服务将如何运行。

可以将其他 `providers` 注入到当前 `provider` 程序函数中，但注入的 `providers` 程序必须在需要它的 `provider` 程序之前定义。

例如：

`$logProvider` 有一个 debugEnabled 方法，它允许你指定 `$log` 服务是否将调试消息记录到控制台。

**API**:

[\$provider.provider(name, provider)](https://docs.angularjs.org/api/auto/service/$provide#provider)

[angular.Module.provider(name, providerType)](https://docs.angularjs.org/api/ng/type/angular.Module#provider)

**使用方法**:

```typescript
namespace app.provideLearning {

  export interface MyClass {
    doSomething(): void;
  }

  export class MyClassWithGet implements MyClass {
    $get() {  // 工厂方法生产带有 doSomething 属性的对象
      console.log('myProvider $get()');
      return new MyClassWithGet();
    }

    doSomething() {
      console.log('MyClassWithGet doSomething()');
    }
  }

  export class MyClassWithoutGet implements MyClass {
    doSomething() {
      console.log('MyClassWithoutGet doSomething()');
    }
  }

  angular.module('provideLearning', [])
  .provider('myProvider1', new MyClassWithGet())    // 方法1：provider 为 new 的对象（第2个参数），该对象必须有一个 $get() 工厂方法
                                                    // 当服务被需要时，$get 方法会被 $injector.invoke()

  .provider('myProvider2', MyClassWithGet)          // 方法2：provider 为构造函数（第2个参数）该构造函数会被 $injector.instantiate() 创建实例，然后被当作对象

  .provider('myProvider3', {                        // 方法3：provider 为 hash 对象（第2个参数），该对象必须有一个 $get() 工厂方法
    // 工厂方法生产带有 doSomething 属性的对象
    $get: () => {
      return new MyClassWithoutGet();
    }
  })

  .provider('myProvider4', {                        // 方法4：provider 为 hash 对象（第2个参数），该对象必须有一个 $get() 工厂方法
    // 工厂方法生产带有 doSomething 属性的 hash 对象
    $get: () => {
      return {
        doSomething: () => {
          console.log('myProvider4 doSomething()');
        }
      }
    }
  })


  .config(($provide: any) => {
    $provide.provider('myProvider5', MyClassWithGet);       // 方法5：provider 为构造函数（第2个参数）该构造函数会被 $injector.instantiate() 创建实例，然后被当作对象
  })


  .config(($provide: any) => {
    $provide.provider('myProvider6', new MyClassWithGet()); // 方法6：provider 为 new 的对象（第2个参数），该对象必须有一个 $get() 工厂方法
                                                            // 当服务被需要时，$get 方法会被 $injector.invoke()
  })

  .config(($provide: any) => {
    $provide.provider('myProvider7', {                      // 方法7：provider 为 hash 对象（第2个参数），该对象必须有一个 $get() 工厂方法
      // 工厂方法生产带有 doSomething 属性的对象
      $get: () => {
        return new MyClassWithoutGet();
      }
    });
  })

  .config(($provide: any) => {
    $provide.provider('myProvider8', {                      // 方法8：provider 为 hash 对象（第2个参数），该对象必须有一个 $get() 工厂方法
      // 工厂方法生产带有 doSomething 属性的 hash 对象
      $get: () => {
        return {
          doSomething: () => {
            console.log('myProvider8 doSomething()');
          }
        }
      }
    });
  })
  .run((myProvider1: MyClass,
        myProvider2: MyClass,
        myProvider3: MyClass,
        myProvider4: MyClass,
        myProvider5: MyClass,
        myProvider6: MyClass,
        myProvider7: MyClass,
        myProvider8: MyClass) => {
    myProvider1.doSomething();  // MyClassWithGet doSomething()
    myProvider2.doSomething();  // MyClassWithGet doSomething()
    myProvider3.doSomething();  // MyClassWithoutGet doSomething()
    myProvider4.doSomething();  // myProvider4 doSomething()
    myProvider5.doSomething();  // MyClassWithGet doSomething()
    myProvider6.doSomething();  // MyClassWithoutGet doSomething()
    myProvider7.doSomething();  // MyClassWithoutGet doSomething()
    myProvider8.doSomething();  // myProvider8 doSomething()
  });
}
```

### factory

注册一个 服务工厂函数，它将被调用以返回服务实例。
factory 是通过 provider 注册服务的缩写形式，不需要像 provider 那样显示写出 `\$get` 属性并为其赋值，
通过 factory 来注册服务，会自动将 服务工厂函数 赋值给 provider 的 `\$get` 属性。

**API**:

[\$provider.factory(name, $getFn)](https://docs.angularjs.org/api/auto/service/$provide#factory)

[angular.Module.factory(name, providerFunction)](https://docs.angularjs.org/api/ng/type/angular.Module#factory)

**使用方法**:

```typescript
namespace app.provideLearning {

  class MyClass {
    doSomething() {
      console.log('MyClass doSomething()');
    }
  }

  angular.module('factoryLearning', [])

  // 方法1：angular 会自动给 $get 属性赋值为 MyClass 构造函数（第二个参数）来组成工厂方法，
  // 当注入 myFactory1 时，会调用 $get 工厂方法得到 MyClass 的实例，
  // 即可以理解为 myFactory1 就是 MyClass 的实例。
  .factory('myFactory1', MyClass)

  // 方法2：angular 会自动给 $get 属性赋值为箭头函数（第二个参数）来组成工厂方法，
  // 当注入 myFactory2 时，会调用 $get 工厂方法得到箭头函数返回的 MyClass 实例，
  // 即可以理解为 myFactory2 就是 MyClass 的实例。
  .factory('myFactory2', () => {
      return new MyClass();
    }
  )

  // 方法3：angular 会自动给 $get 属性赋值为箭头函数（第二个参数）来组成工厂方法，
  // 当注入 myFactory3 时，会调用 $get 工厂方法得到箭头函数返回的带有 doSomething 属性的 hash 对象，
  // 即可以理解为 myFactory3 就是 带有 doSomething 属性的 hash 对象。
  .factory('myFactory3', () => {
    return {
      doSomething: () => {
        console.log('myFactory3 doSomething()');
      }
    }
  })

  // 方法4：angular 会自动给 $get 属性赋值为箭头函数（第二个数组参数中的箭头函数）来组成工厂方法，
  // 这里可以使用数组的形式来注入其他服务。
  // 当注入 myFactory4 时，会调用 $get 工厂方法得到箭头函数返回的带有 doSomething 属性的 hash 对象，
  // 即可以理解为 myFactory4 就是 带有 doSomething 属性的 hash 对象。
  .factory('myFactory4', ['$filter', ($filter: any) => {
    return {
      doSomething: () => {
        console.log('myFactory4 uses filter ' + $filter('currency')(1));
      }
    }
  }])

  // 方法5：angular 会自动给 $get 属性赋值为箭头函数（第二个参数，这里简称 箭头函数1）来组成工厂方法，
  // 这里可以不使用数组的形式来注入其他服务。
  // 当注入 myFactory5 时，会调用 $get 工厂方法得到 箭头函数1 返回的另一个箭头函数（这里简称 箭头函数2），
  // 即可以理解为 myFactory5 就是 箭头函数2，可以通过 myFactory5() 来调用。
  .factory('myFactory5', ($filter: any) => {
    return () => {
      return {
        doSomething: () => {
          console.log('myFactory5 uses filter ' + $filter('currency')(2));
        }
      }
    }
  })

  // 方法6：angular 会自动给 $get 属性赋值为 MyClass 构造函数（第二个参数）来组成工厂方法，
  // 当注入 myFactory6 时，会调用 $get 工厂方法得到 MyClass 的实例，
  // 即可以理解为 myFactory6 就是 MyClass 的实例。
  .config(($provide: any) => {
    $provide.factory('myFactory6', MyClass);
  })

  // 方法7：angular 会自动给 $get 属性赋值为箭头函数（第二个参数）来组成工厂方法，
  // 当注入 myFactory7 时，会调用 $get 工厂方法得到箭头函数返回的 MyClass 实例，
  // 即可以理解为 myFactory7 就是 MyClass 的实例。
  .config(($provide: any) => {
    $provide.factory('myFactory7', () => {
        return new MyClass();
      }
    );
  })

  // 方法8：angular 会自动给 $get 属性赋值为箭头函数（第二个参数）来组成工厂方法，
  // 当注入 myFactory8 时，会调用 $get 工厂方法得到箭头函数返回的带有 doSomething 属性的 hash 对象，
  // 即可以理解为 myFactory8 就是 带有 doSomething 属性的 hash 对象。
  .config(($provide: any) => {
    $provide.factory('myFactory8', () => {
      return {
        doSomething: () => {
          console.log('myFactory8 doSomething()');
        }
      }
    });
  })

  // 方法9：angular 会自动给 $get 属性赋值为箭头函数（第二个数组参数中的箭头函数）来组成工厂方法，
  // 这里可以使用数组的形式来注入其他服务。
  // 当注入 myFactory9 时，会调用 $get 工厂方法得到箭头函数返回的带有 doSomething 属性的 hash 对象，
  // 即可以理解为 myFactory9 就是 带有 doSomething 属性的 hash 对象。
  .config(($provide: any) => {
    $provide.factory('myFactory9', ['$filter', ($filter: any) => {
      return {
        doSomething: () => {
          console.log('myFactory9 uses filter ' + $filter('currency')(3));
        }
      }
    }]);
  })

  // 方法10：angular 会自动给 $get 属性赋值为箭头函数（第二个参数，这里简称 箭头函数1）来组成工厂方法，
  // 这里可以不使用数组的形式来注入其他服务。
  // 当注入 myFactory10 时，会调用 $get 工厂方法得到 箭头函数1 返回的另一个箭头函数（这里简称 箭头函数2），
  // 即可以理解为 myFactory10 就是 箭头函数2，可以通过 myFactory10() 来调用。
  .config(($provide: any) => {
    $provide.factory('myFactory10', ($filter: any) => {
      return  () => {
        return {
          doSomething: () => {
            console.log('myFactory10 uses filter ' + $filter('currency')(4));
          }
        }
      }
    });
  })

  .run((myFactory1: any,
        myFactory2: any,
        myFactory3: any,
        myFactory4: any,
        myFactory5: any,
        myFactory6: any,
        myFactory7: any,
        myFactory8: any,
        myFactory9: any,
        myFactory10: any) => {
    myFactory1.doSomething();     // MyClass doSomething()
    myFactory2.doSomething();     // MyClass doSomething()
    myFactory3.doSomething();     // myFactory3 doSomething()
    myFactory4.doSomething();     // myFactory4 uses filter $1.00
    myFactory5().doSomething();   // myFactory5 uses filter $2.00
    myFactory6.doSomething();     // MyClass doSomething()
    myFactory7.doSomething();     // MyClass doSomething()
    myFactory8.doSomething();     // myFactory8 doSomething()
    myFactory9.doSomething();     // myFactory9 uses filter $3.00
    myFactory10().doSomething();  // myFactory10 uses filter $4.00
  });
}
```

### service

注册一个 service 构造函数，它将被 new 调用以创建服务实例。
service 是通过 provider 注册服务的缩写形式，不需要像 provider 那样显示写出 `\$get` 属性并为其赋值，
通过 service 来注册服务，angular 会自动使用一个 工厂函数 来包装构造函数的实例化并返回该实例。

内部看起来有点像这样:
```javascript
{
  $get: function() { // 工厂函数
    return $injector.instantiate(constructor); // 构造函数实例化并返回该实例
  }
}
```

**API**:

[\$provider.service(name, constructor);](https://docs.angularjs.org/api/auto/service/$provide#service)

[angular.Module.service(name, constructor);](https://docs.angularjs.org/api/ng/type/angular.Module#service)

**使用方法**:

```typescript
namespace app.serviceLearning {

  class MyClass {
    doSomething() {
      console.log('MyClass doSomething()');
    }
  }

  class MyClassWithInject {

    // 注入依赖 $filter
    static readonly $inject: string[] = ['$filter'];

    constructor(private readonly $filter: any) {

    }

    doSomething() {
      console.log('MyClassWithInject use filter: ' + this.$filter('currency')(1));
    }
  }

  angular.module('serviceLearning', [])

  // 方法1：直接传递一个 Class（构造函数），
  // angular 会自动给 $get 属性赋值为 Class 构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService1 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService1 就是 Class 的实例。
  .service('myService1', MyClass)

  // 方法2：直接传递一个 构造函数（其实也是一个普通方法），
  // angular 会自动给 $get 属性赋值为构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService2 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService2 就是 构造函数 的实例。
  .service('myService2', function AConstructor(this: any) {
    this.doSomething = () => {
      console.log('myService2 uses constructor function');
    }
  })

  // 方法3：传递一个 Class（构造函数），该 Class 注入了其他服务（$filter），
  // angular 会自动给 $get 属性赋值为 Class 构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService3 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService3 就是 Class 的实例。
  .service('myService3', MyClassWithInject)

  // 方法4：传递一个数组，数组中包含一个 构造函数（其实也是一个普通方法），该 构造函数 注入了其他服务（$filter），
  // angular 会自动给 $get 属性赋值为构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService4 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService4 就是 构造函数 的实例。
  .service('myService4', ['$filter', function AConstructor(this: any, $filter: any) {
    this.doSomething = () => {
      console.log('myService4 uses constructor function and use filter: ' + $filter('currency')(1));
    }
  }])

  // 方法5：直接传递一个 Class（构造函数），
  // angular 会自动给 $get 属性赋值为 Class 构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService5 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService5 就是 Class 的实例。
  .config(($provide: any) => {
    $provide.service('myService5', MyClass);
  })

  // 方法6：直接传递一个 构造函数（其实也是一个普通方法），
  // angular 会自动给 $get 属性赋值为构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService6 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService6 就是 构造函数 的实例。
  .config(($provide: any) => {
    $provide.service('myService6', function AConstructor(this: any) {
      this.doSomething = () => {
        console.log('myService6 uses constructor function');
      }
    });
  })

  // 方法7：传递一个 Class（构造函数），该 Class 注入了其他服务（$filter），
  // angular 会自动给 $get 属性赋值为 Class 构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService7 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService7 就是 Class 的实例。
  .config(($provide: any) => {
    $provide.service('myService7', MyClassWithInject);
  })

  // 方法8：传递一个数组，数组中包含一个 构造函数（其实也是一个普通方法），该 构造函数 注入了其他服务（$filter），
  // angular 会自动给 $get 属性赋值为构造函数（第二个参数）来组成工厂方法，
  // 当注入 myService8 时，会调用 $get 工厂方法得到 构造函数 的实例，
  // 即可以理解为 myService8 就是 构造函数 的实例。
  .config(($provide: any) => {
    $provide.service('myService8', ['$filter', function AConstructor(this: any, $filter: any) {
      this.doSomething = () => {
        console.log('myService8 uses constructor function and use filter: ' + $filter('currency')(1));
      }
    }]);
  })

  .run((myService1: any,
        myService2: any,
        myService3: any,
        myService4: any,
        myService5: any,
        myService6: any,
        myService7: any,
        myService8: any) => {
    myService1.doSomething();     // MyClass doSomething()
    myService2.doSomething();     // myService2 uses constructor function
    myService3.doSomething();     // MyClassWithInject use filter: $1.00
    myService4.doSomething();     // myService4 uses constructor function and use filter: $1.00
    myService5.doSomething();     // MyClass doSomething()
    myService6.doSomething();     // myService6 uses constructor function
    myService7.doSomething();     // MyClassWithInject use filter: $1.00
    myService8.doSomething();     // myService8 uses constructor function and use filter: $1.00
  });
}
```

### value

使用 `$injector` 注册一个 value 服务，例如字符串、数字、数组、对象或函数。
value 是通过 provider 注册服务的缩写形式，不需要像 provider 那样显示写出 `\$get` 属性并为其赋值，
并且这里隐式的 `\$get` 属性是一个工厂函数，但其不接受参数，返回值为 value 服务。这也意味着不可能将其他服务注入到 value 服务中。

**注意**：

value 服务类似于 constant 服务，不同之处是 value **不能被注入到模块的配置函数中**(参见 angular.Module)，并且 value 可以被 AngularJS 的装饰器（decorator）覆盖。

**API**:

[\$provider.value(name, value);](https://docs.angularjs.org/api/auto/service/$provide#value)

[angular.Module.value(name, object);](https://docs.angularjs.org/api/ng/type/angular.Module#value)

**使用方法**:

```typescript
namespace app.valueLearning {

  class MyClass {
    doSomething() {
      console.log('My value is a class');
    }
  }

  angular.module('valueLearning', [])

  // 传递一个类（构造函数），
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 Class 构造函数（第二个参数），
  // 当注入 myValue1 时，会调用 $get 工厂方法并得到返回的 MyClass 构造函数（第二个参数），
  // 即可以理解为 myValue1 就是 Class。
  .value('myValue1', MyClass)

  // 传递一个构造函数，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该构造函数（第二个参数），
  // 当注入 myValue2 时，会调用 $get 工厂方法并得到返回的该构造函数，
  // 即可以理解为 myValue2 就是这个构造函数（第二个参数）。
  .value('myValue2', function AConstructor(this: any) {
    this.doSomething = () => {
      console.log('myValue2 return constructor function');
    }
  })

  // 传递一个字符串，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该字符串（第二个参数），
  // 当注入 myValue3 时，会调用 $get 工厂方法并得到返回的该字符串，
  // 即可以理解为 myValue3 就是这个字符串（第二个参数）。
  .value('myValue3', 'my Value 3 is a string')

  // 传递一个数组，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该数组（第二个参数），
  // 当注入 myValue4 时，会调用 $get 工厂方法并得到返回的该数组，
  // 即可以理解为 myValue4 就是这个数组（第二个参数）。
  .value('myValue4', ['my', 'value', '4', 'is', 'an', 'array'])

  // 传递一个 hash 对象，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 hash 对象（第二个参数），
  // 当注入 myValue5 时，会调用 $get 工厂方法并得到返回的该 hash 对象，
  // 即可以理解为 myValue5 就是这个 hash 对象（第二个参数）。
  .value('myValue5', {
    value: 'my value5 is an object'
  })


  // 传递一个类（构造函数），
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 Class 构造函数（第二个参数），
  // 当注入 myValue1 时，会调用 $get 工厂方法并得到返回的 MyClass 构造函数（第二个参数），
  // 即可以理解为 myValue1 就是 Class。
  .config(($provide: any) => {
    $provide.value('myValue6', MyClass);
  })

  // 传递一个构造函数，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该构造函数（第二个参数），
  // 当注入 myValue2 时，会调用 $get 工厂方法并得到返回的该构造函数，
  // 即可以理解为 myValue2 就是这个构造函数（第二个参数）。
  .config(($provide: any) => {
    $provide.value('myValue7', function AConstructor(this: any) {
      this.doSomething = () => {
        console.log('myValue7 is a constructor function');
      }
    });
  })

  // 传递一个字符串，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该字符串（第二个参数），
  // 当注入 myValue3 时，会调用 $get 工厂方法并得到返回的该字符串，
  // 即可以理解为 myValue3 就是这个字符串（第二个参数）。
  .config(($provide: any) => {
    $provide.value('myValue8', 'my value 8 is string');
  })

  // 传递一个数组，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该数组（第二个参数），
  // 当注入 myValue4 时，会调用 $get 工厂方法并得到返回的该数组，
  // 即可以理解为 myValue4 就是这个数组（第二个参数）。
  .config(($provide: any) => {
    $provide.value('myValue9', ['my', 'value', '9', 'is', 'an', 'array']);
  })

  // 传递一个 hash 对象，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 hash 对象（第二个参数），
  // 当注入 myValue5 时，会调用 $get 工厂方法并得到返回的该 hash 对象，
  // 即可以理解为 myValue5 就是这个 hash 对象（第二个参数）。
  .config(($provide: any) => {
    $provide.value('myValue10', {
      value: 'my value10 is an object'
    });
  })

  // myValue10 不能被注入到模块的配置函数中。
  .config(($provide: any, myValue10: any) => {
    $provide.value('myValue11', {
      value: 'my value11 is an object'
    });
  })

  .run((myValue1: any,
        myValue2: any,
        myValue3: any,
        myValue4: any,
        myValue5: any,
        myValue6: any,
        myValue7: any,
        myValue8: any,
        myValue9: any,
        myValue10: any) => {

    new myValue1().doSomething(); // My value is a class (使用 new 来实例化)
    new myValue2().doSomething(); // myValue2 return constructor function （使用 new 来实例化）
    console.log(myValue3);        // my Value 3 is a string
    console.log(myValue4);        // ['my', 'value', '4', 'is', 'an', 'array']
    console.log(myValue5.value);  // my value5 is an object

    new myValue6().doSomething(); // My value is a class (使用 new 来实例化)
    new myValue7().doSomething(); // myValue7 return constructor function （使用 new 来实例化）
    console.log(myValue8);        // my Value 8 is a string
    console.log(myValue9);        // ['my', 'value', '9', 'is', 'an', 'array']
    console.log(myValue10.value);  // my value10 is an object
  });
}
```

### constant

使用 `$injector` 注册一个 constant  服务，例如字符串、数字、数组、对象或函数。
与通过 value 注册服务一样，constant 是通过 provider 注册服务的缩写形式，不需要像 provider 那样显示写出 `\$get` 属性并为其赋值，
并且这里隐式的 `\$get` 属性是一个工厂函数，但其不接受参数，返回值为 constant 服务。这也意味着不可能将其他服务注入到 constant 服务中。

**注意**：

constant 服务类似于 constant 服务，不同之处是 constant **能被注入到模块的配置函数中**(参见 angular.Module)，并且 constant 不可以被 AngularJS 的装饰器（decorator）覆盖。

**API**:

[\$provider.constant(name, value);](https://docs.angularjs.org/api/auto/service/$provide#constant)

[angular.Module.constant(name, object);](https://docs.angularjs.org/api/ng/type/angular.Module#constant)

**使用方法**:

```typescript
namespace app.constantLearning {

  class MyClass {
    doSomething() {
      console.log('My constant is a class');
    }
  }

  angular.module('constantLearning', [])

  // 传递一个类（构造函数），
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 Class 构造函数（第二个参数），
  // 当注入 myConstant1 时，会调用 $get 工厂方法并得到返回的 MyClass 构造函数（第二个参数），
  // 即可以理解为 myConstant1 就是 Class。
  .constant('myConstant1', MyClass)

  // 传递一个构造函数，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该构造函数（第二个参数），
  // 当注入 myConstant2 时，会调用 $get 工厂方法并得到返回的该构造函数，
  // 即可以理解为 myConstant2 就是这个构造函数（第二个参数）。
  .constant('myConstant2', function AConstructor(this: any) {
    this.doSomething = () => {
      console.log('myConstant2 return constructor function');
    }
  })

  // 传递一个字符串，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该字符串（第二个参数），
  // 当注入 myConstant3 时，会调用 $get 工厂方法并得到返回的该字符串，
  // 即可以理解为 myConstant3 就是这个字符串（第二个参数）。
  .constant('myConstant3', 'my Constant 3 is a string')

  // 传递一个数组，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该数组（第二个参数），
  // 当注入 myConstant4 时，会调用 $get 工厂方法并得到返回的该数组，
  // 即可以理解为 myConstant4 就是这个数组（第二个参数）。
  .constant('myConstant4', ['my', 'constant', '4', 'is', 'an', 'array'])

  // 传递一个 hash 对象，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 hash 对象（第二个参数），
  // 当注入 myConstant5 时，会调用 $get 工厂方法并得到返回的该 hash 对象，
  // 即可以理解为 myConstant5 就是这个 hash 对象（第二个参数）。
  .constant('myConstant5', {
    constant: 'my constant5 is an object'
  })


  // 传递一个类（构造函数），
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 Class 构造函数（第二个参数），
  // 当注入 myConstant1 时，会调用 $get 工厂方法并得到返回的 MyClass 构造函数（第二个参数），
  // 即可以理解为 myConstant1 就是 Class。
  .config(($provide: any) => {
    $provide.constant('myConstant6', MyClass);
  })

  // 传递一个构造函数，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该构造函数（第二个参数），
  // 当注入 myConstant2 时，会调用 $get 工厂方法并得到返回的该构造函数，
  // 即可以理解为 myConstant2 就是这个构造函数（第二个参数）。
  .config(($provide: any) => {
    $provide.constant('myConstant7', function AConstructor(this: any) {
      this.doSomething = () => {
        console.log('myConstant7 is a constructor function');
      }
    });
  })

  // 传递一个字符串，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该字符串（第二个参数），
  // 当注入 myConstant3 时，会调用 $get 工厂方法并得到返回的该字符串，
  // 即可以理解为 myConstant3 就是这个字符串（第二个参数）。
  .config(($provide: any) => {
    $provide.constant('myConstant8', 'my constant 8 is string');
  })

  // 传递一个数组，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该数组（第二个参数），
  // 当注入 myConstant4 时，会调用 $get 工厂方法并得到返回的该数组，
  // 即可以理解为 myConstant4 就是这个数组（第二个参数）。
  .config(($provide: any) => {
    $provide.constant('myConstant9', ['my', 'constant', '9', 'is', 'an', 'array']);
  })

  // 传递一个 hash 对象，
  // 当调用 $get 属性对应的工厂方法时，angular 会返回该 hash 对象（第二个参数），
  // 当注入 myConstant5 时，会调用 $get 工厂方法并得到返回的该 hash 对象，
  // 即可以理解为 myConstant5 就是这个 hash 对象（第二个参数）。
  .config(($provide: any) => {
    $provide.constant('myConstant10', {
      constant: 'my constant10 is an object'
    });
  })

  // myConstant10 可以被注入到模块的配置函数中。
  .config(($provide: any, myConstant10: any) => {
    $provide.constant('myConstant11', {
      constant: 'my constant11 is an object',
      myConstant10: myConstant10
    });
  })

  .run((myConstant1: any,
        myConstant2: any,
        myConstant3: any,
        myConstant4: any,
        myConstant5: any,
        myConstant6: any,
        myConstant7: any,
        myConstant8: any,
        myConstant9: any,
        myConstant10: any,
        myConstant11: any) => {

    new myConstant1().doSomething(); // My constant is a class (使用 new 来实例化)
    new myConstant2().doSomething(); // myConstant2 return constructor function （使用 new 来实例化）
    console.log(myConstant3);        // my Constant 3 is a string
    console.log(myConstant4);        // ['my', 'constant', '4', 'is', 'an', 'array']
    console.log(myConstant5.constant);  // my constant5 is an object

    new myConstant6().doSomething(); // My constant is a class (使用 new 来实例化)
    new myConstant7().doSomething(); // myConstant7 return constructor function （使用 new 来实例化）
    console.log(myConstant8);        // my Constant 8 is a string
    console.log(myConstant9);        // ['my', 'constant', '9', 'is', 'an', 'array']
    console.log(myConstant10.constant);  // my constant10 is an object
    console.log(myConstant11.constant);  // my constant11 is an object
    console.log(myConstant11.myConstant10.constant);  // my constant10 is an object
  });
}
```

### decorator


使用 `$injector` 注册一个 decorator  服务。
decorator 函数会拦截原始服务的创建，它被允许覆盖或修改原始服务的行为。decorator 函数的返回值可以是原始服务，也可以是替换(或包装和委托)原始服务的新服务

**API**:

[\$provider.decorator(name, decorator);](https://docs.angularjs.org/api/auto/service/$provide#decorator)

[angular.Module.constant(name, object);](https://docs.angularjs.org/api/ng/type/angular.Module#decorator)

**使用方法**

[How to use decorators](https://docs.angularjs.org/guide/decorators)

**使用方法**:

```typescript
namespace app.serviceLearning {

  class MyClass {
    doSomething() {
      console.log('MyClass doSomething()');
    }
  }

  class MyDecoratorClass {
    doSomething() {
      console.log('MyDecoratorClass doSomething()');
    }
  }

  angular.module('decoratorLearning', [])

  .service('myService1', MyClass)

  // 使用 MyDecoratorClass 来替换 myService1
  .decorator('myService1', MyDecoratorClass)

  .service('myService1_1', MyClass)

  // 使用 构造函数 来包装 myService1_1
  .decorator('myService1_1',  ['$delegate', function AConstructor($delegate: MyClass) {
    // 返回被包装的原始服务
    return $delegate;
  }])
  .config(($provide: any) => {
    $provide.service('myService2', MyClass);
  })

  // 使用 MyDecoratorClass 来替换 myService2
  .config(($provide: any) => {
    $provide.service('myService2', MyDecoratorClass);
  })

  .run((myService1: any,
        myService1_1: any,
        myService2: any) => {
    myService1.doSomething();     // MyDecoratorClass doSomething()
    myService1_1.doSomething();   // MyClass doSomething()
    myService2.doSomething();     // MyDecoratorClass doSomething()
  });
}
```


## 参考

[官方文档 - Services](https://docs.angularjs.org/guide/services)

[官方API - angular.Module](https://docs.angularjs.org/api/ng/type/angular.Module#angular.Module-methods)

[官方API - \$provide](https://docs.angularjs.org/api/auto/service/$provide)