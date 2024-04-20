---
title: 如何从Angularjs 升级到 Angular
date: 2022/01/01
tags:
  - web
  - angularjs
  - Angular
categories:
  - web
reward: true
---

## 1. 前言

原文: [Upgrading from AngularJS to Angular](https://angular.io/guide/upgrade#using-angularjs-component-directives-from-angular-code)

Author: AngularJS 官方

译者：[philoenglish.com](https://philoenglish.com) 团队

关键字： Angularjs Angular Angular1.x Angular2.x migration 迁移， 升级

这里的 Angular 是指 Angular 2.x, 而 AngularJS 是指 AngularJS 1.x 版本。 Angular (通常是指 "Angular 2+" 或 "Angular v2 及更高版本") 是一个基于 TypeScript 的 开源 Web 应用框架 由 Google 的 Angular 团队以及社区共同领导。Angular 是由 AngularJS 的同一个开发团队完全重写的。

<!-- more -->

## 2. Angular 和 AngularJS 之间的区别

在设计上，Angular 是 AngularJS 的完全重写。

- Angular 没有“作用域”或控制器的概念，其架构中的主要角色是一些层次化的组件。
- Angular 具有不同的表达式语法，主要是用 "[ ]" 来表示属性绑定，以及用 "( )" 来表示事件绑定
- 模块化 – 许多核心功能都已模块化
- Angular 建议使用 Microsoft 的 TypeScript 语言，该语言引入了如下特性：
  - 静态类型，包括 泛型
  - 装饰器，语法上类似于注解
- TypeScript 是 ECMAScript 6 (ES6) 的超集，并且与 ECMAScript 5 (即: JavaScript) 向下兼容。
- 动态加载
- 异步模板编译
- RxJS 提供了迭代式回调。RxJS 在状态可见性和调试方面有局限，不过可以使用诸如 ngReact 或 ngrx 之类的响应式第三方库来解决这些问题
- 支持 Angular Universal，它可以在服务器上运行 Angular 应用程序

## 3. 升级注意事项

- 虽然 AngularJS 版本很低了，但是用 AngularJS 应用程序很棒。在迁移到 Angular 之前，请务必根据自身的实际情况考虑是否升级的必要性， 以及所需花费的时间和精力是否合算。 本指南介绍了用于将 AngularJS 项目高效迁移到 Angular 平台的内置工具。

- 某些应用程序将比其他应用程序更容易升级，并且有很多方法可以使自己更容易升级。甚至可以在开始升级过程之前准备 AngularJS 应用程序并将其与 Angular 的技术栈， 架构模式对齐。这些准备步骤都是为了让代码更分离、更易于维护，并且更好地与现代开发工具保持一致。这意味着除了使升级更容易之外，您还将改进现有的 AngularJS 应用程序。

- 成功升级的关键之一是以增量方式进行升级，方法是在同一应用程序中并行运行两个框架，并将 AngularJS 组件逐个移植到 Angular。这样，即使是大型和复杂的应用程序，也可以在不中断其他业务的情况下进行升级，因为工作可以协作完成，并且可以在一段时间内分散。
  Angular 中的升级模块旨在使增量升级无缝衔接。

## 4. 升级前准备工作

- 当前有很多方法可以规划 AngularJS 应用程序的结构。当您开始将这些应用程序升级到 Angular 时，可以考虑将一些面向未来的工具或应用程序先应用到当前的 AngularJS 应用程序上，这样会使得迁移过程比不使用这些工具或程序或使用其它蹩脚的工具更轻松一些。

## 5. 遵循 AngularJS 风格指南

[AngularJS 风格指南](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md)收集了已被证明可以产生更简洁、更易于维护的 AngularJS 应用程序的模式和实践。它包含了大量有关如何编写和组织 AngularJS 代码的资料以及一些反例。

Angular 是 AngularJS 最佳部分的重新构想版本。从这个意义上说，它的目标与 AngularJS 的风格指南相同：保留 AngularJS 的良好部分，并避免坏部分。 遵循风格指南有助于使您的 AngularJS 应用程序与 Angular 更加紧密地保持一致,

其中有一些规则可以使得通过 Angular upgrade/static 模块模式进行增量升级变得更加轻松：

- 规则 1： 一个组件应该只有一个文件。 这样不仅使得导航和查找组件更容易易，而且还允许一次性将其迁移到不同语言不同框架。在此示例应用程序中，每个控制器、组件、服务和筛选器都位于其自己的源文件中。
- 规则 2： 文件夹按功能组织文件和模块化规则， 不同的功能模块应该存放在一起， 不同功能模块应该在不同的模块中， 功能应该是判断相识性的依据。

当应用程序以功能特性组织时，可以一次迁移一个功能。即使不是为了升级， 这也是对规划应用的结构有益的忠告。 对于不符合 AngularJS 风格指南的应用程序， 强烈建议是先遵循 AngularJS 风格指南， 再开始迁移。

## 6. 使用打包工具

当您将应用程序打散为一个组件一个文件后， 您通常最终会得到一个包含大量较小的文件的项目结构。这是一种比少量大文件更整洁的组织方式，但是如果您必须将所有这些文件加载到浏览器时，则效果不佳，特别是当您还必须维护他们之间的依赖关系时时。这就是为什么需要开始使用打包工具的原因。

使用诸如 SystemJS， Webpack， gulp， grunt 或 Browserify 之类的打包工具，我们可以使用 TypeScript 或 ES2015 的内置模块系统。您可以使用导入和导出功能，这些功能显式指定哪些代码可以并且将在应用程序的不同部分之间共享。对于 ES5 应用程序，您可以使用 CommonJS 样式要求和 module.exports 功能。在这两种情况下，模块加载器将负责以正确的顺序加载应用程序所需的所有代码。

在将应用投入生产时，模块装载机还可以更轻松地将它们全部打包到成 bundle 包。

## 7. 迁移到 typescript

如果 Angular 升级计划的一部分是同时使用 TypeScript，那么甚至在升级本身开始之前引入 TypeScript 编译器也是有意义的。这意味着在实际升级过程中，需要学习和思考的事情少了一件事。这也意味着你可以开始在 AngularJS 代码中使用 TypeScript 功能。
由于 TypeScript 是 ECMAScript 2015 的超集，而 ECMAScript 2015 又是 ECMAScript 5 的超集，因此"切换到"TypeScript 并不一定需要安装 TypeScript 编译器并将文件从 _.js 重命名为_.ts。但是，当然，仅仅这样做并不是非常有用或令人兴奋。诸如下面的其他步骤可以给我们带来更多的收益：

- 对于使用 module loader 的应用程序，TypeScript 导入和导出（实际上是 ECMAScript 2015 导入和导出）可用于将代码组织到模块中。
- 类型注释可以逐渐添加到现有函数和变量中，以固定其类型并获得诸如构建时错误检查，出色的自动完成支持和内联文档等好处。
- ES2015 的 JavaScript 功能，如箭头函数，lets 和 consts，默认函数参数和解构赋值也可以逐步添加，以使代码更具表现力。
- service 和 controller 可以转换为类。这样，它们将更接近成为 Angular 服务和组件类，这将使升级更轻松。

## 8. 使用 Component Directives

在 Angular 中，Component 是构建用户界面的主要组成部分。将 UI 的不同部分定义为组件，并将它们组合成完整的页面。

您也可以在 AngularJS 中使用 Component directive 做相同的事情。 这些是定义自己的模板，控制器和输入/输出绑定的指令 - 与 Angular 组件定义的内容相同。与使用 ng 控制器、ng 包含和作用域继承等较低级别功能构建的应用程序相比，使用组件指令构建的应用程序更容易迁移到 Angular。

### 8.1. 要与 Angular 兼容，AngularJS Component directive 应该配置如下属性

- restrict: 'E'组件通常用作元素。

- scope: {} - 隔离作用域。在 Angular 中，组件始终与周围环境隔离，您也可以在 AngularJS 中执行此操作。

- bindToController： {}.组件输入和输出应绑定到控制器，而不是使用$scope。

- controller 和 controllerAs， 组件有自己的控制器。

- template or templateUrl， 组件有自己的模板。

### 8.2. Component directive 还可以使用以下属性

- transclude：true/{}，如果组件需要超越来自其他地方的内容。
- require，如果组件需要与某个父组件的控制器进行通信。

### 8.3. 组件指令不应使用以下属性

- compile, 这在 Angular 中不受支持。

- replace: true, Angular 从不将组件元素替换为组件模板。此属性在 AngularJS 中也被弃用。

- priority 和 terminal。虽然 AngularJS 组件可以使用这些，但它们不在 Angular 中使用，最好不要再使用这两个属性。

### 8.4. 一个能与 Angular 完全一致的 AngularJS Component directive 示例

```js
hero - detail.directive.ts;
export function heroDetailDirective() {
  return {
    restrict: "E",
    scope: {},
    bindToController: {
      hero: "=",
      deleted: "&",
    },
    template: `
      <h2>{{$ctrl.hero.name}} details!</h2>
      <div><label>id: </label>{{$ctrl.hero.id}}</div>
      <button ng-click="$ctrl.onDelete()">Delete</button>
    `,
    controller: function HeroDetailController() {
      this.onDelete = () => {
        this.deleted({ hero: this.hero });
      };
    },
    controllerAs: "$ctrl",
  };
}
```

从 AngularJS 1.5 开始引入了 Component，可以更轻松地定义此类 Component directive。将此 Component API 用于组件指令是一个好主意，原因如下：

- 它需要较少的样板代码。
- 它强制使用 controllerAs 这种组件的最佳实践。
- 它具有更好的默认值， 例如 scope 和 restrict 等为指令属性。

### 8.5. 上面的 Component directive 示例在使用 Component 表示时如下所示

```js
export const heroDetail = {
  bindings: {
    hero: "<",
    deleted: "&",
  },
  template: `
    <h2>{{$ctrl.hero.name}} details!</h2>
    <div><label>id: </label>{{$ctrl.hero.id}}</div>
    <button ng-click="$ctrl.onDelete()">Delete</button>
  `,
  controller: function HeroDetailController() {
    this.onDelete = () => {
      this.deleted(this.hero);
    };
  },
};
```

Controller 生命周期钩子方法$onInit（）、$onDestroy（）和$onChanges（）是从 AngularJS 1.5 开始引入的其他方便的功能。它们在 Angular 中几乎都有完全相同的功能，因此围绕它们组织组件生命周期逻辑将简化最终的 Angular 升级过程。

## 9. 借助 ngUpgrade 包进行升级工作

Angular 中的 ngUpgrade 包是一个非常有用的升级工具。有了它，您可以在同一应用程序中混合使用 AngularJS 和 Angular 组件，并使它们无缝衔接。这意味着您不必一次完成所有升级工作，因为在过渡期间，两个框架之间存在自然共存。

> AngularJS 的生命周期将于 2021 年 12 月 31 日结束。ngUpgrade 现在处于功能完整可用状态。我们继续发布 ngUpgrade 的安全和错误修复直到 2022 年 12 月 31 日。

### 9.1. ngUpgrade 工作原理

ngUpgrade 提供的主要工具之一称为 UpgradeModule。这是一个包含用于引导和支持 Angular 和 AngularJS 混合开发的实用程序模块。

当你使用 ngUpgrade 时，你真正要做的是同时运行 AngularJS 和 Angular。所有 Angular 代码都在 Angular 框架中运行，AngularJS 代码在 AngularJS 框架中运行。这两者都拥有框架的完整功能和特性。不是模拟仿真，因此您可以期望同时拥有两个框架的所有功能和自然行为。

除此之外，由一个框架管理的组件和服务可以与另一个框架中的组件和服务进行交互。这主要包括：依赖注入、DOM 和数据感知。

#### 9.1.1. 依赖注入

依赖注入在 AngularJS 和 Angular 中都是前端开发的前沿技术和核心功能，但是这两个框架在实际工作方式上存在一些重要的差异。  
| ANGULARJS | ANGULAR |
| :---- | :---- |
| 依赖关系注入的 tokens 始终是字符串 | 依赖关系注入的 tokens 可以具有不同的类型。它们通常是类, 它们也可能是字符串。|
| 全局仅有一个 injector。即使在多模块应用程序中，所有内容都导入一个大的命名空间中. | 有一个 injector 的树状结构，每个组件都有一个根 injector 和一个附加的 injector。 |

即使有这样大的差异，您仍然可以拥有依赖注入互操作性。upgrade/static 解决了这些差异，使一切可以无缝工作：
您可以通过升级 AngularJS service 来将其注入 Angular 代码。每个单例服务能在框架之间共享。在 Angular 中，这些服务将始终位于根注入器中，并可供所有组件使用。

您也可以通过降级 Angular 服务来将其注入 AngularJS 代码。只有来自 Angular 根注入器的服务才能降级。同样，相同的单例实例服务在框架之间共享。注册降级的服务时，必须显式指定要在 AngularJS 中使用的字符串 token。

#### 9.1.2. 组件 和 DOM

在混合模式的 DOM 中，有来自 AngularJS 的组件和指令也有 Angular 的组件和指令。这些组件通过使用各自框架的双向通道相互通信，通过 ngUpgrade 组件进行桥接。也还可以通过共享的对象进行通信， 比如共享服务。

关于混合应用程序，要了解的关键是： DOM 中的每个元素都由两个框架中的一个拥有。另一个框架忽略了它。如果一个元素归 AngularJS 所有，Angular 会将其视为不存在，反之亦然。

因此，通常混合应用程序作为 AngularJS 应用程序开始启动，由 AngularJS 处理 root template，例如，index.html。然后，当遇到 Angular 指令或组件时 Angular 才参与进来， 相应指令或组件后续也由 Angular 负责管理,即使 template 包含任意数量的 Angular 组件和指令， 也能被管理起来。

除此之外，两个框架之间的组件还可以进行交互。 您通过以下两种方式之一跨越两个框架之间的边界：

通过使用一个框架元素使用另一个框架中的元素， 例如 在 AngularJS 的模板中使用 Angular 组件 ，或在 Angular 模板中使用 AngularJS 组件。

通过包含或投影来自其他框架的内容。ngUpgrade 将 AngularJS transclusion 和 Angular 内容投影的相关概念桥接在一起。

每当在一个框架的模板中使用属于另一个框架的组件时，都会在框架边界之间发生切换。但是，这种切换仅发生在该组件的模板中的元素上， 不会涉及到其他部分。
例如使用 AngularJS 模板中的 Angular 定义的组件的场景，如下所示：

```html
<a-component></a-component>
```

虽然 DOM 元素`<a-component>`是一个 Angular 组件指令， 但是此元素将由 AngularJS 托管，因为它是在 AngularJS 模板中使用的。这也意味着您也可以将其替换为其他 AngularJS 指令，但不能替换为 Angular 指令。
但是在`<a-component>`的模板中内部，Angular 将会介入其中， 其元素内部可以使用 Angular 组件， 当然也可以使用 AngularJS 组件。 当您使用 Angular 模板中使用 AngularJS 组件指令时，以上同样的规则也适用 Angular 模板， 以此类推。

### 9.2. 修改数据感知方式

`scope.$apply()` 是 AngularJS 检测更改和更新数据绑定的方式。在发生每个事件后，将调用 `scope.$apply()` 这由框架自动完成，或由您手动完成。

在 Angular 中，事情是不同的。虽然更改检测仍然在每次事件发生后发生，但需要调用 `scope.$apply()` 即可实现此目的。这是因为所有 Angular 代码都在称为 Angular Zone 中运行。Angular 总是知道代码何时完成，因此它也知道何时应该启动更改检测。代码本身不必调用 `scope.$apply()` 或类似的东西。

在混合应用程序中，UpgradeModule 桥接了 AngularJS 和 Angular 方法。以下是发生的情况：

应用程序中发生的所有事情都在 Angular 区域内运行。无论事件起源于 AngularJS 还是 Angular 代码，都是如此。该区域会在每次事件发生被 Angular 感知到。

在 Angular Zone 每一轮代码执行之后， UpgradeModule 都会调用 AngularJS 的 $rootScope.$apply（）方法 。 还会在每次事件发生后触发 AngularJS 更改检测。

实际上您不需要调用 `$apply()`，无论它是在 AngularJS 还是 Angular 中。UpgradeModule 为我们做到了这一点。您仍然可以调用 `$apply()`，因此无需从现有代码中删除此类调用。这些调用只会在混合应用程序中触发额外的 AngularJS 更改检测检查。

当使用降级 Angular 组件，然后从 AngularJS 使用组件时，将使用 AngularJS 更改检测来监视该组件的输入。当这些输入发生更改时，将设置组件中的相应属性。您还可以通过在组件中实现 OnChanges 接口来挂钩到更改中，就像在组件中未降级时一样。

相应地，当您升级 AngularJS 组件并从 Angular 使用它时，为组件指令的 scope （或 bindToController）定义的所有绑定都将挂接到 Angular 更改检测中。它们将被视 Angular inputs。当它们变化时，它们的值将写入已升级组件的作用域（或控制器）。

### 9.3. 使用 UpgradeModule 结合 Angular NgModules

AngularJS 和 Angular 都有自己的模块概念，以帮助将应用程序组织成内聚的功能块。

在体系结构和实现方面有很大的不同, 它们有细节上的差异。在 AngularJS 中，将服务，控制器等添加到 angular.module 属性。
而在 Angular 中，您可以创建一个或多个带有 NgModule decorator 的类，来定义模块和它们的依赖关系。差异从那里开始的。

在混合应用程序中，您至少需要一个来自 AngularJS 和一个来自 Angular 的模块。您需要在 NgModule 中导入 UpgradeModule，然后使用它来加载 AngularJS 模块。

### 9.4. 启动混合应用

要引导混合应用程序，必须引导应用程序的每个 Angular 和 AngularJS 部件。您必须首先引导 Angular 然后让 UpgradeModule 接下来引导 AngularJS 。

在 AngularJS 应用中，你有一个根模块，根模块也将用于引导 AngularJS 应用。  
app.module.ts

```js
angular.module("heroApp", []).controller("MainCtrl", function () {
  this.message = "Hello world";
});
```

纯 AngularJS 应用程序可以通过在 HTML 页面上的某个地方使用 ng-app 指令自动引导。但对于混合应用程序，您需要自己写代码使用 UpgradeModule 来引导。因此，修改 AngularJS 应用使用代码调用 angular.bootstrap 进行引导是一个很好的初步步骤， 做好是能在升级到混合应用之前这样引导。

假设您当前的引导方式是这样的，

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <base href="/" />
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.3/angular.js"></script>
    <script src="app/ajs-ng-app/app.module.js"></script>
  </head>

  <body ng-app="heroApp" ng-strict-di>
    <div id="message" ng-controller="MainCtrl as mainCtrl">
      {{ mainCtrl.message }}
    </div>
  </body>
</html>
```

你可以从 HTML 中删除 ng-app 和 ng-strict-di 指令，而是切换到从 JavaScript 调用 angular.bootstrap，像这样：

```js
angular.bootstrap(document.body, ["heroApp"], { strictDi: true });
```

要开始将 AngularJS 应用程序转换为混合应用程序，您需要加载 Angular 框架。 至于如何加载 Angular 框架,可以参考这篇文章[Setup for Upgrading to AngularJS](https://angular.io/guide/upgrade-setup)中的详细说明。 根据项目的需求，从项目[QuickStart github repository](https://github.com/angular/quickstart)拷贝一些代码来使用.

您还需要使用 npm 安装@angular/upgrade --save 来安装@angular/upgrade 软件包，并为@angular/upgrade/static 软件包添加映射：

要构建混合应用， 有一个 npm 包是必须安装的， 他们分别是@angular/upgrade

```bash
 npm install @angular/upgrade --save
```

然后要在配置文件中添加一条映射：
systemjs.config.js

```yaml
'@angular/upgrade/static': 'npm:@angular/upgrade/fesm2015/static.mjs',
```

接着要创建一个模块文件 app.module.ts， 引入并配置 NgModule class

```ts
import { NgModule } from "@angular/core";
import { BrowserModule } from "@angular/platform-browser";
import { UpgradeModule } from "@angular/upgrade/static";

@NgModule({
  imports: [BrowserModule, UpgradeModule],
})
export class AppModule {
  constructor(private upgrade: UpgradeModule) {}
  ngDoBootstrap() {
    this.upgrade.bootstrap(document.body, ["heroApp"], { strictDi: true });
  }
}
```

在 NgModule decorator 配置中， 至少需要导入 BrowserModule, 这是每个基于 Angular 浏览器的应用程序都必须具有的模块, 还从@angular/upgrade/static 导入 UpgradeModule，UpgradeModule 用于升级和降级服务和组件需要用到的模块。

在 AppModule 的构造函数中，使用依赖关系注入来获取 UpgradeModule 实例，并使用它来引导 AppModule.ngDoBootstrap 方法中的 AngularJS 应用程序。upgrade.bootstrap 方法采用与 angular.bootstrap 完全相同的参数

> 注意： 您无需将 bootstrap 声明添加到@NgModule 装饰器上，因为 AngularJS 将掌管应用程序的 root template。

现在，您可以使用 AppModuleBrowserDynamic.bootstrapModule 方法引导 AppModule。

```ts
import { platformBrowserDynamic } from "@angular/platform-browser-dynamic";

platformBrowserDynamic().bootstrapModule(AppModule);
```

这一步完成后， 恭喜你！您正在运行混合应用程序！现有的 AngularJS 代码与以前一样工作，您已准备好开始添加 Angular 代码。

## 10. 在 AngularJS 代码使用 Angular Components

运行混合应用后，可以开始逐步升级代码。执行此操作的更常见模式之一是在 AngularJS 上下文中使用 Angular 组件。这可能是一个全新的组件，或者以前是 AngularJS 但已经为 Angular 重写的组件。

假设您有一个 Angular 组件如下，用于显示有关 Hero 的信息：  
hero-detail.component.ts

```ts
import { Component } from "@angular/core";

@Component({
  selector: "hero-detail",
  template: `
    <h2>Windstorm details!</h2>
    <div><label>id: </label>1</div>
  `,
})
export class HeroDetailComponent {}
```

如果你想在 Anguarjs 代码中使用这个组件, 你需要使用 downgradeComponent()方法先将 angular 组件降级, 得到一个 Angular 一个 AngularJS 的 directive 后将它注册到 AngularJS module, 这样就可以在 AngularJS 模板中使用了.

```ts
import { HeroDetailComponent } from "./hero-detail.component";

/* . . . */

import { downgradeComponent } from "@angular/upgrade/static";

angular.module("heroApp", []).directive(
  "heroDetail",
  downgradeComponent({
    component: HeroDetailComponent,
  }) as angular.IDirectiveFactory
);
```

> 默认情况下，对于每个 AngularJS $digest 周期， Angular 数据感知将在组件上运行。如果只想在输入更改时运行数据感知，则可以在调用 downgradeComponent（）时将 propagateDigest 设置为 false。

由于 HeroDetailComponent 是一个 Angular 组件，因此您还必须将其添加到 AppModule 中的声明中。

```ts
import { HeroDetailComponent } from "./hero-detail.component";

@NgModule({
  imports: [BrowserModule, UpgradeModule],
  declarations: [HeroDetailComponent],
})
export class AppModule {
  constructor(private upgrade: UpgradeModule) {}
  ngDoBootstrap() {
    this.upgrade.bootstrap(document.body, ["heroApp"], { strictDi: true });
  }
}
```

> 所有 Angular 组件、指令和管道都必须在 NgModule 中声明。

最终结果是一个名为 heroDetail 的 AngularJS 指令创建成功，您可以像 AngularJS 模板中的任何其他指令一样使用它。

```html
<hero-detail></hero-detail>
```

> 注意： heroDetail 是一个 AngularJS 元素指令（restrict：‘E’）。AngularJS 元素指令根据其名称进行匹配。降级的 Angular 组件的 selector 元数据将被忽略。

当然，大多数组件都不是这么简单。他们中的许多人都有 input 和 output，将他们与外部世界联系起来。具有输入和输出的 Angular hero 组件可能如下所示：

```ts
import { Component, EventEmitter, Input, Output } from "@angular/core";
import { Hero } from "../hero";

@Component({
  selector: "hero-detail",
  template: `
    <h2>{{ hero.name }} details!</h2>
    <div><label>id: </label>{{ hero.id }}</div>
    <button (click)="onDelete()">Delete</button>
  `,
})
export class HeroDetailComponent {
  @Input() hero!: Hero;
  @Output() deleted = new EventEmitter<Hero>();
  onDelete() {
    this.deleted.emit(this.hero);
  }
}
```

这些输入和输出可以从 AngularJS 模板提供，downgradeComponent()（）方法负责连接它们：

即使您位于 AngularJS 模板中，您也使用 Angular 属性语法来绑定输入和输出。这是降级组件的要求。表达式本身仍然是 AngularJS 正则表达式。

### 10.1. 使用降级组件的 KEBAB-CASE 写法

对于降级的组件使用 Angular 属性语法的规则时，有一个值得注意的例外。当属性名称由多个单词组成时。在 Angular 中，您可以使用 camelCase 绑定这些属性：

```code
[myHero]="hero"
(heroDeleted)="handleHeroDeleted($event)"
```

但是，在 AngularJS 模板中使用它们时，您必须使用 kebab-case：

```code
[my-hero]="hero"
(hero-deleted)="handleHeroDeleted($event)"
```

看出差别来了吗？ AngularJS 模板中使用中划线分割， 而 Angular 是驼峰风格。

由于这是一个 AngularJS template，您仍然可以在元素上使用其他 AngularJS 指令，即使它具有 Angular 绑定属性。例如，您可以使用 ng-repeat 轻松创建组件的多个副本：

```html
<div ng-controller="MainController as mainCtrl">
  <hero-detail
    [hero]="hero"
    (deleted)="mainCtrl.onDelete($event)"
    ng-repeat="hero in mainCtrl.heroes"
  >
  </hero-detail>
</div>
```

## 11. 在 Angular 代码使用 AngularJS Components

因此，您可以编写一个 Angular 组件，然后从 AngularJS 代码中使用它。当您开始从较低级别的组件迁移并逐步向上移动时，这很有用。但在某些情况下，以相反的顺序做事会更方便：从更高级别的组件开始，然后向下工作。这也可以使用 upgrade/static 来完成。您可以升级 AngularJS 组件指令，然后从 Angular 使用它们。

并非所有种类的 AngularJS 指令都可以升级。该指令实际上必须是组件指令，具有[准备指南](https://angular.io/guide/upgrade#using-component-directives)中描述的特征。为了确保兼容性， 最安全的选择是使用 AngularJS 1.5 或以上的版本。

下面是一个可升级组件示例，该组件仅具有模板和控制器：

```ts
export const heroDetail = {
  template: `
    <h2>Windstorm details!</h2>
    <div><label>id: </label>1</div>
  `,
  controller: function HeroDetailController() {},
};
```

您可以使用 UpgradeComponent 类将此组件升级到 Angular 元素。 方法是通过继承 UpgradeComponent 来创建 Angular 指令并在其构造函数执行父类的构造方法， 这样您将拥有一个完全升级的 AngularJS 组件，可在 Angular 内部使用。剩下的就是将其添加到 AppModule 的声明数组中。

```ts
import { Directive, ElementRef, Injector, SimpleChanges } from "@angular/core";
import { UpgradeComponent } from "@angular/upgrade/static";

@Directive({
  selector: "hero-detail",
})
export class HeroDetailDirective extends UpgradeComponent {
  constructor(elementRef: ElementRef, injector: Injector) {
    super("heroDetail", elementRef, injector);
  }
}
```

```ts
@NgModule({
  imports: [BrowserModule, UpgradeModule],
  declarations: [
    HeroDetailDirective,
    /* . . . */
  ],
})
export class AppModule {
  constructor(private upgrade: UpgradeModule) {}
  ngDoBootstrap() {
    this.upgrade.bootstrap(document.body, ["heroApp"], { strictDi: true });
  }
}
```

> 升级后的组件是一个 Angular 指令，而不是组件，因为 Angular 不知道 AngularJS 将在它下面创建元素。据 Angular 所知，升级后的组件只是一个指令——一个标签——Angular 不必关心它的元素内部的内容。

升级后的组件还可以具有输入和输出，如原始 AngularJS 组件指令的作用域/控制器绑定所定义。使用 Angular 模板中的组件时，请按照以下规则使用 Angular 模板语法提供输入和输出：
| | BINDING DEFINITION | TEMPLATE SYNTAX |
| :---- | :---- | :---- |
| Attribute binding | myAttribute: '@myAttribute' | `<my-component myAttribute="value">` |
|Expression binding | myOutput: '&myOutput' | `<my-component (myOutput)="action()">` |
|One-way binding | myValue: '<myValue' | `<my-component [myValue]="anExpression">` |
|Two-way binding | myValue: '=myValue' | As a two-way binding: `<my-component [(myValue)]="anExpression">` Since most AngularJS two-way bindings actually only need a one-way binding in practice, `<my-component [myValue]="anExpression">` is often enough.|

例如，想象一个带有一个输入和一个输出的英雄细节 AngularJS 组件指令：

```ts
export const heroDetail = {
  bindings: {
    hero: "<",
    deleted: "&",
  },
  template: `
    <h2>{{$ctrl.hero.name}} details!</h2>
    <div><label>id: </label>{{$ctrl.hero.id}}</div>
    <button ng-click="$ctrl.onDelete()">Delete</button>
  `,
  controller: function HeroDetailController() {
    this.onDelete = () => {
      this.deleted(this.hero);
    };
  },
};
```

您可以将此组件升级到 Angular，在升级指令中注释输入和输出，然后使用 Angular 模板语法提供输入和输出：

```ts
import {
  Directive,
  ElementRef,
  Injector,
  Input,
  Output,
  EventEmitter,
} from "@angular/core";
import { UpgradeComponent } from "@angular/upgrade/static";
import { Hero } from "../hero";

@Directive({
  selector: "hero-detail",
})
export class HeroDetailDirective extends UpgradeComponent {
  @Input() hero: Hero;
  @Output() deleted: EventEmitter<Hero>;

  constructor(elementRef: ElementRef, injector: Injector) {
    super("heroDetail", elementRef, injector);
  }
}
```

## 12. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 13. 参考文档

[Upgrading from AngularJS to Angular](https://angular.io/guide/upgrade)

[Angular 官网](https://angular.io/)

[Angular 官方文档](https://angular.io/docs/ts/latest/quickstart.html)

[Angular 中文文档](https://angular.cn/docs)

[Angular 教程\_Angular8 Angular9 Angular12 入门实战视频教程-2021 年更新【IT 营】](https://www.bilibili.com/video/BV1bt411e71b?p=1)

[Angular1.x + TypeScript 编码风格](https://segmentfault.com/a/1190000015291731)

[@component 无法注入\_详解 Angular 依赖注入](https://blog.csdn.net/weixin_36265390/article/details/112118141)
