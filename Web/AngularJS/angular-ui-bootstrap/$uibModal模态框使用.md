# $uibModal 模态框的使用

## 介绍

$uibModal 是一个创建模态窗口的 service。

$uibModal service 只有一个方法: open(options)。options 参数是一个 hash 对象，可以使用它来对模态框进行配置。

## .open(options) 方法

### open 方法的 options 参数

* **animation** (类型: boolean, 默认: true) - 设置为 false 时将禁用模态框/背景的动画。
  对于已经显示的的模态框/背景，将不触发其动画。

* **appendTo** (类型: angular.element, 默认: body: 示例: $document.find('aside').eq(0)) - 将模态附加到特定元素中。

* **ariaDescribedBy** (类型: string) - 在模态上设置 aria-describedby 属性。
  该值应该是一个 id 值(不需要 # 符号)，指向描述模态的元素，例如 .modal-body 所在的元素。
  通常，这将指向模态上的文本，但不包括用户将与之交互的东西，如按钮或表单。省略此选项不会影响正常用户，
  但会影响对可访问性支持，即残障人士的使用。

* **ariaLabelledBy** (类型: string) - 设置模态上的 aria-labeledby 属性。该值应该是一个 id 值(不需要 # 符号)，指向标记模态的元素。
  通常，这将指向模态上的标题元素。省略此选项不会影响正常用户，但会影响对可访问性支持，即残障人士的使用。

* **backdrop** (类型: boolean|string, 默认: true) - 控制背景显示与否。
  可使用的值有：true(默认)，false(不需要背景)，'static'(禁用通过点击背景来关闭模态框功能)。

* **backdropClass** (类型: string) - 可添加 CSS class 到模态框背景的模板上。

* **bindToController** (类型: boolean, 默认: false) - 当和 controllerAs 一起使用并将 bindToController 的值设置为 true 时，
  它将把 $scope 属性绑定到控制器上。

* **component** (类型: string, 示例: 'myComponent') - 一个指向将要被渲染的 component 的字符串引用，
  并且这个 component 需要是注册在了 Angular 的编译器中。
  如果使用指令，该指令必须被设置为 restrict: 'E'，同时 template 或者 templateUrl 也必须被设置。

  在 component 中支持以下绑定:

    * **close** - 可用于关闭模态框并传递数据的方法。当需要传递数据时，其格式必须为 {\$value: myResult}，
      即以这样的方式调用 close({\$value: myResult})

    * **dismiss** - 可用于解散（dismiss）模态框并传递数据的方法。当需要传递数据时，其格式必须为 {\$value: myRejectedResult}，
      即以这样的方式调用 dismiss({\$value: myRejectedResult})

    * **modalInstance** - 模态框实例。这与使用 controller 时注入的 $uibModalInstance 相同。

    * **resolve** - 模态框解析值的对象。详情请参见 [UI Router resolve](https://angular-ui.github.io/bootstrap/#ui-router-resolves)。

* **controller** (类型: function|string|array, 示例: 'MyModalController') - 模态框实例的 controller，
  可以是字符串形式的 controller 名称，也可以是内联控制器函数，可以选择使用数组来封装以使用依赖注入。
  允许 controller-as 语法。可以通过 $uibModalInstance 注入来访问模态框实例。

* **controllerAs** (类型: string, 示例: 'ctrl') - controller-as 语法的一种替代方法。也需要提供 controller 选项。

* **keyboard** - (类型: boolean, 默认: true) - 表明模态框是否可以通过按 ESC 键来关闭。

* **openedClass** (类型: string, 默认: 'modal-open') - css class，在打开模态框时添加到 body 元素上。

* **resolve** (类型: Object) - 被解析并传递给 controller 的子级作用域的数据。它相当于路由器中的 resolve 属性。

* **scope** (类型: $scope) - 被用于模态框内容中的父级作用域实例。默认是 $rootScope。

* **size** (类型: string, 示例: 'lg') - 模态框窗口 class 的可选后缀。值将被与 modal- 进行拼接，
  即如果值为 sm，那么拼接后的class为 modal-sm。

* **template** (类型: string) - 表明模态框将使用的内联模板，其类容为该选项的值，例如 `template: '<span>模态框内容</span>'`。

* **templateUrl** (类型: string) - 表明模态框内容的模板路径。template 与 templateUrl 只能使用其一，不能同时使用。

* **windowClass** (类型: string) - 要添加到模态框窗口模板的附加 CSS class(es)。

* **windowTemplateUrl** (类型: string, 默认: 'uib/template/modal/window.html') - 使用该路径对应的模板来覆盖模态框窗口模板。

* **windowTopClass** (类型: string) -  添加到顶部模态框窗口的 CSS class(es)。

**注意**：可以通过 $uibModalProvider.options 为 $uibModal 来设置全局默认值。

### open 方法的返回值

open 方法返回一个模态框实例，该实例对象具有以下属性:

* **close(result)** (类型: function) - 可以用来关闭模态框，可以选择传递结果数据。

* **dismiss(reason)** (类型: function) - 可以用来关闭模态框，可以选择传递理由。

* **result** (类型: promise) - 当模态框是被 close() 方法关闭时其状态为 resolved，当模态框是被 dismiss() 方法关闭时其状态为 rejected。

* **opened** (类型: promise) - 当模态框在下载内容模板并解析所有变量后被打开，其状态为 resolved。

* **closed** (类型: promise) - 当模态框被关闭并且关闭动画完成后，其状态为 resolved。

* **rendered** (类型: promise) - 当模态框被渲染后，其状态为 resolved。

**注意**：

关闭(close 或者 dismiss)可以直接在模态框的模板中使用，其使用方法如下：

* **$close(result)** (类型: function)  - 可以用来关闭模态框，可以选择传递结果数据。

* **$dismiss(reason)** (类型: function) - 可以用来关闭模态框，可以选择传递理由。

这两个方法使关闭模态框窗口变得很容易，因为其不需要创建一个专用控制器。

使用方法：
```html
<button class="btn btn-primary" type="button" ng-click="$close($ctrl.result)">OK</button>
```

**close 与 dismiss 的区别**

当模态框是被 close() 方法关闭时模态框实例的 result 属性对应的 promise 状态为 resolved，当模态框是被 dismiss() 方法关闭时则其状态为 rejected。

## 将 angular-ui-bootstrap 引入项目

要使用 $uibModal 则需要将 angular-ui-bootstrap 依赖引入项目中。

如果使用 Node.js 进行依赖的管理，在 package.json 文件中使用以下代码引入依赖：

```json
"devDependencies": {
  "@types/angular": "1.8.1",
  "@types/jquery": "3.3.29",
  "@types/angular-ui-bootstrap": "1.0.0" 
}

"dependencies": {
  "angular": "1.8.2",
  "angular-animate": "1.8.2",
  "angular-sanitize": "1.8.2",
  "angular-ui-bootstrap": "^2.5.6",
  "bootstrap": "^5.1.3",
  "jquery": "3.6.0"
}
```

## 使用 $uibModal

当所有依赖包都引入到项目与页面中后，则需要将 ui.bootstrap 以模块依赖的形式引入到模块当中：

```javascript
angular.module('myModule', ['ui.bootstrap']);
```

在创建 controller 时需要引入 '$uibModal' 服务。

```TypeScript
class MyController {
  static readonly $inject: string[] = ['$uibModal'];

  constructor(private readonly $uibModal: ng.ui.bootstrap.IModalService) {
  }
}
```

### 举例

Html 代码：
```html
<!DOCTYPE html>
<html ng-app="myModule">
<head>
  <meta charset='utf-8'>
  <title>$uibModal 示例</title>
  <link rel='stylesheet' type='text/css' media='screen' href='../node_modules/angular-ui-bootstrap/dist/ui-bootstrap-csp.css'>
  <link rel='stylesheet' type='text/css' media='screen' href='../node_modules/bootstrap/dist/css/bootstrap.min.css'>

  <script src='../node_modules/jquery/dist/jquery.js'></script>
  <script src='../node_modules/angular/angular.js'></script>
  <script src='../node_modules/angular-animate/angular-animate.js'></script>
  <script src='../node_modules/angular-sanitize/angular-sanitize.js'></script>
  <script src='../node_modules/angular-ui-bootstrap/dist/ui-bootstrap-tpls.js'></script>
  <script src='../dist/app.js'></script>  <!-- 引入app.ts被编译后的app.js -->
</head>
<body ng-controller="MyController as $ctrl">
  <button ng-click="$ctrl.openModal()">Open Modal</button>
  <div>
    <span>Selected item:</span><span>{{$ctrl.selectedItem}}</span>
  </div>

  <script type="text/ng-template" id="myModalContent.html">
    <div class="modal-header">
        <h3 class="modal-title" id="modal-title">I'm a modal!</h3>
    </div>
    <div class="modal-body" id="modal-body">
        <ul>
            <li ng-repeat="item in $ctrl.items">
                <a href="#" ng-click="$event.preventDefault(); $ctrl.selected.item = item">{{ item }}</a>
            </li>
        </ul>
        Selected: <b>{{ $ctrl.selected.item }}</b>
    </div>
    <div class="modal-footer">
        <button class="btn btn-primary" type="button" ng-click="$ctrl.ok()">OK</button>
        <button class="btn btn-warning" type="button" ng-click="$ctrl.cancel()">Cancel</button>
    </div>
  </script>
</body>
</html>
```

TypeScript 代码(app.ts)：
```typescript
const app = angular.module('myModule', ['ui.bootstrap']);

class MyController {
  static readonly $inject: string[] = [
    '$uibModal'
  ];

  items: any;
  selectedItem: any;

  constructor(private readonly $uibModal: ng.ui.bootstrap.IModalService) {
  }

  $onInit() {
    this.items = ['item1', 'item2', 'item3'];
  }

  openModal = () => {
    const modalInstance = this.$uibModal.open({
      animation: false,
      ariaLabelledBy: 'modal-title',
      ariaDescribedBy: 'modal-body',
      templateUrl: 'myModalContent.html',
      controller: 'ModalInstanceCtrl',
      controllerAs: '$ctrl',
      size: 'lg',
      appendTo: $('body'),
      resolve: {
        items: () => {
          return this.items;
        }
      }
    });

    modalInstance.result.then((result) => {
      this.selectedItem = result;
    })
  }
}

class ModalInstanceCtrl {
  static readonly $inject: string[] = [
    '$uibModalInstance',
    'items'
  ];

  selected: any;

  constructor(private readonly $uibModalInstance: ng.ui.bootstrap.IModalInstanceService,
              private readonly items: any) {
  }

  $onInit() {
    this.selected = {
      item: this.items[0]
    };
  }

  ok(): void {
    this.$uibModalInstance.close(this.selected.item);
  };

  cancel(): void {
    this.$uibModalInstance.dismiss('cancel');
  };
}

angular.module('myModule').controller('MyController', MyController);
angular.module('myModule').controller('ModalInstanceCtrl', ModalInstanceCtrl);
```

## $uibModal 单元测试

对以上举例中 MyController 类进行单元测试：

```typescript
describe('openModal', function(this: any) {
  beforeEach(() => {
    angular.mock.module('ui.bootstrap');
    angular.mock.module('myModule');

    angular.mock.inject(($controller, $uibModal, $q, $rootScope) => {
      this.$uibModal = $uibModal;
      this.$q = $q;
      this.$rootScope = $rootScope;
      this.controller = $controller('MyController', {
        $uibModal: this.$uibModal
      });
    });

    spyOn(this.$uibModal, 'open');
  })

  it('should call open()', () => {
    const result = {
      selectedItem : 'item1'
    }
    this.$uibModal.open.and.callFake((options: any) => {
      expect(options).toEqual({
        animation: false,
        ariaLabelledBy: 'modal-title',
        ariaDescribedBy: 'modal-body',
        templateUrl: 'myModalContent.html',
        controller: 'ModalInstanceCtrl',
        controllerAs: '$ctrl',
        size: 'lg',
        appendTo: $('body'),
        resolve: {
          items: jasmine.any(Function)
        }
      });
      expect(options.resolve.items()).toEqual(this.controller.items);   // 测试 resolve.items() 方法
      return {
        result: this.$q.resolve(result)
      };
    });

    this.controller.openModal();

    this.$rootScope.$digest();

    expect(this.$uibModal.open).toHaveBeenCalled();
    expect(this.controller.selectedItem).toEqual(result);
  })
});
```

## 参考

[官方文档 - Angular directives for Bootstrap](https://angular-ui.github.io/bootstrap/)