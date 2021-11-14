# angular.mock.module() 的使用

## 介绍

* angular.mock.module() 这个函数在 window 全局变量上，以便于访问，也可以直接通过 module() 进行调用。
* 此函数仅在使用 jasmine 或 mocha 运行单元测试时才会被声明。

每个模块在每个注入器中只能加载一次。AngularJS 应用通常只有一个注入器，而且模块只加载一次。每个测试都有自己的注入器，并且会多次加载模块。

## 举例

逻辑代码：
```javascript
let app = angular.module('greetMod', []);

app.factory('alert', function($window) {
  return function(text) {
    $window.alert(text);
  }
})

app.value('salutation', 'Hello');

app.factory('greet', function(alert, salutation) {
  return function(name) {
    alert(salutation + ' ' + name + '!');
  }
});
```

单元测试代码：
```javascript
describe('myApp', function() {
  // 加载程序 module (`greetMod`) 之后加载一个指定的测试模块，
  // 该测试模块重写了 `$window` ，这个 `$window` 是一个被mock的版本，
  // 因此当调用 `window.alert()` 时，不会被真的时 alert 弹框被阻塞。
  beforeEach(() => {
    module('greetMod', ($provide) => {
      $provide.value('$window', {
        alert: jasmine.createSpy('alert')
      });
    })
  });

  // inject() 将创建一个 injector 并且将 `greet` 与 `$window` 注入到单元测试中。
  it('should alert on $window', inject((greet, $window) => {
    greet('World');
    expect($window.alert).toHaveBeenCalledWith('Hello World!');
  }));

  // 以下时另一个再单元测试中重写配置的方法。在代码行中使用 `module` 和 `inject` 方法。
  it('should alert using the alert service', () => {
    var alertSpy = jasmine.createSpy('alert');
    module(($provide) => {
      $provide.value('alert', alertSpy);
    });
    inject((greet) => {
      greet('World');
      expect(alertSpy).toHaveBeenCalledWith('Hello World!');
    });
  });
});
```

## 参考

[官方文档 - Unit Testing](https://docs.angularjs.org/guide/module#unit-testing)

[官方文档 - API](https://docs.angularjs.org/api/ngMock/function/angular.mock.module)