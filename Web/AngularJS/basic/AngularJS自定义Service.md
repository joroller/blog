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

**API**:

[\$provider.provider(name, provider)](https://docs.angularjs.org/api/auto/service/$provide#provider)

[angular.Module.provider(name, providerType)](https://docs.angularjs.org/api/ng/type/angular.Module#provider)


### factory

### service

### value

### constant

### decorator


## 参考

[官方文档 - Services](https://docs.angularjs.org/guide/services)

[官方API - angular.Module](https://docs.angularjs.org/api/ng/type/angular.Module#angular.Module-methods)

[官方API - \$provide](https://docs.angularjs.org/api/auto/service/$provide)