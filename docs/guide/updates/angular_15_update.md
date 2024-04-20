---
title: Angular 15 有哪些更新?
order: 100
---

在过去的一年里，我们删除了 Angular 的旧版编译器和渲染管道，这使得在过去几个月内实现了一系列开发人员体验的改进。Angular v15 是这方面的巅峰之作，它进行了数十项改进，带来了更好的开发人员体验和性能。

<!-- more -->

## 1. Standalone APIs 现已从开发者预览版毕业

在 v14 中，我们引入了新的 [Standalone](https://philoenglish.com/query/Standalone) API，使开发人员能够在不使用 NgModule 的情况下构建应用程序。我们很高兴地告诉大家，这些 API 已从开发者预览版毕业，现在已成为稳定 API 的一部分。从现在开始，我们将按照语义版本控制逐步发展它们。

作为确保独立 API 准备好毕业的一部分，我们确保独立组件可以跨 Angular 工作，并且它们现在可以在 HttpClient, Angular Elements、路由等中完全工作。

独立 API 允许您使用单个组件引导应用程序：

```ts
import { bootstrapApplication } from "@angular/platform-browser";
import { ImageGridComponent } from "./image-grid";

@Component({
  standalone: true,
  selector: "photo-gallery",
  imports: [ImageGridComponent],
  template: ` … <image-grid [images]="imageList"></image-grid> `,
})
export class PhotoGalleryComponent {
  // component logic
}

bootstrapApplication(PhotoGalleryComponent);
```

## 2. Router 和 HttpClient tree-shakable standalone API

您可以使用新的路由器独立 API 构建多路由应用程序！要声明根路由，您可以使用以下命令：

```ts
export const appRoutes: Routes = [
  {
    path: "lazy",
    loadChildren: () =>
      import("./lazy/lazy.routes").then((routes) => routes.lazyRoutes),
  },
];
```

lazyRoutes 声明于：

```ts
import { Routes } from "@angular/router";

import { LazyComponent } from "./lazy.component";

export const lazyRoutes: Routes = [{ path: "", component: LazyComponent }];
```

最后，appRoutes 注册在 bootstrapApplication 中：

```ts
bootstrapApplication(AppComponent, {
  providers: [provideRouter(appRoutes)],
});
```

API 的另一个好处 provideRouter 是它时 tree-shakable 的！Bundlers 可以在构建时删除路由器未使用的功能。在使用新 API 进行的测试中，我们发现从 Bundle 包中删除这些未使用的功能可以使应用程序捆绑包中的路由器代码大小减少 11%。

## 3. 组合指令 API

指令组合 API 将代码重用提升到另一个水平！此功能的灵感来自 GitHub 上最受欢迎的[功能请求](https://github.com/angular/angular/issues/8785)，要求提供向 host 元素添加指令的功能。

组合指令 API 使开发人员能够使用指令增强 host 元素，并为 Angular 配备强大的代码重用策略，这要归功于我们的编译器。指令组合 API 仅适用于独立指令。

让我们看一个简单的例子：

```ts
@Component({
  selector: "mat-menu",
  hostDirectives: [
    HasColor,
    {
      directive: CdkMenu,
      inputs: ["cdkMenuDisabled: disabled"],
      outputs: ["cdkMenuClosed: closed"],
    },
  ],
})
class MatMenu {}
```

在上面的代码片段中，我们 MatMenu 使用两个指令进行增强：HasColor 和 CdkMenu。MatMenu 重用 HasColor 的所有输入、输出和关联逻辑，并且仅重用来自 CdkMenu 的选定输入和输出逻辑。

这种技术可能会让您想起某些编程语言中的多重继承或特征，不同之处在于我们有解决名称冲突的机制，并且它适用于用户界面原语。

## 4. 图像指令现已稳定

我们发布了 Angular 图像指令的开发者预览版，该指令是我们与 Chrome [Aurora](https://philoenglish.com/query/Aurora) 在 v14.2 中合作开发的。

我们很高兴地告诉大家，它现在已经稳定了！Land's End 对此功能进行了实验，并在灯塔实验室测试中观察到 LCP 提高了 75% 。

v15 版本还包括一些针对 image 指令的新功能：

- 自动 srcset 生成：该指令通过为您生成属性来确保请求适当大小的图像。这可以减少图像的下载时间。

- 填充模式[实验特性]：此模式使图像填充其父容器，从而无需声明图像的宽度和高度。如果您不知道图像的大小或者想要迁移 CSS 背景图像以使用该指令，那么这是一个方便的工具。

NgOptimizedImage 您可以直接在组件或 NgModule 中使用独立指令：

```ts

import { NgOptimizedImage } from '@angular/common';

// Include it into the necessary NgModule
@NgModule({
  imports: [NgOptimizedImage],
})
class AppModule {}

// ... or a standalone Component
@Component({
  standalone: true
  imports: [NgOptimizedImage],
})
class MyStandaloneComponent {}

```

要在组件中使用它，只需将图像的 src 属性替换为 ngSrc，并确保为 LCP 图像指定 priority 属性。

您可以在[我们的文档](https://angular.io/guide/image-directive)中找到更多信息。

## 5. 函数式路由器守卫

与 tree-shakable 独立路由器 API 一起使用时，我们致力于减少防护中的模板文件。让我们看一个示例，其中我们定义了一个守卫来验证用户是否已登录：

```ts
@Injectable({ providedIn: "root" })
export class MyGuardWithDependency implements CanActivate {
  constructor(private loginService: LoginService) {}

  canActivate() {
    return this.loginService.isLoggedIn();
  }
}

const route = {
  path: "somePath",
  canActivate: [MyGuardWithDependency],
};
```

LoginService 实现了大部分逻辑，在守卫中我们只调用 isLoggedIn(). 尽管守卫非常简单，但我们有很多样板代码。

使用新的功能性路由器守卫，您可以将此代码重构为：

```ts
const route = {
  path: "admin",
  canActivate: [() => inject(LoginService).isLoggedIn()],
};
```

我们在守卫声明中表达了整个守卫逻辑。函数式路由器守卫也是可组合的——您可以创建类似工厂的函数来接受配置并返回防护或解析器函数。您可以在 GitHub 上找到[串行运行路由器防护的示例](https://github.com/angular/angular/blob/8546b17adec01de69bf314a959ef2d12f6638eb9/packages/router/test/integration.spec.ts#L5157-L5194)。

## 6. Router unwraps 默认导入

为了使路由器更简单并进一步减少样板文件，路由器现在在延迟加载时自动解包默认导出。

假设您有以下内容 LazyComponent：

```ts

@Component({
  standalone: true,
  template: '...'
})
export default class LazyComponent { ... }

```

在此更改之前，要延迟加载独立组件，您必须：

```ts

{
  path: 'lazy',
  loadComponent: () => import('./lazy-file').then(m => m.LazyComponent),
}

```

现在，路由器将查找默认导出，如果找到，则自动使用它，这将路由声明简化为：

```ts
{
  path: 'lazy',
  loadComponent: () => import('./lazy-file'),
}
```

## 7. 更好的堆栈跟踪

我们从年度开发者调查中获得了很多见解，因此我们要感谢您花时间分享您的想法！深入研究开发人员面临的调试体验难题，我们发现错误消息需要一些改进。

Angular 开发人员的调试难题

我们与 Chrome DevTools 合作解决了这个问题！让我们看一下您可能在 Angular 应用程序上使用的示例堆栈跟踪：

```bash
ERROR Error: Uncaught (in promise): Error
Error
    at app.component.ts:18:11
    at Generator.next (<anonymous>)
    at asyncGeneratorStep (asyncToGenerator.js:3:1)
    at _next (asyncToGenerator.js:25:1)
    at _ZoneDelegate.invoke (zone.js:372:26)
    at Object.onInvoke (core.mjs:26378:33)
    at _ZoneDelegate.invoke (zone.js:371:52)
    at Zone.run (zone.js:134:43)
    at zone.js:1275:36
    at _ZoneDelegate.invokeTask (zone.js:406:31)
    at resolvePromise (zone.js:1211:31)
    at zone.js:1118:17
    at zone.js:1134:33
```

这段代码有两个主要问题：

- 只有一行与开发人员编写的代码相对应。其他一切都来自第三方依赖项（Angular 框架、Zone.js、RxJS）
- 没有关于什么用户交互导致错误的信息

Chrome DevTools 团队创建了一种机制，通过 Angular CLI 注释源映射来忽略来自 node_modules 的脚本。我们还合作开发了异步堆栈标记 API，该 API 允许我们将独立的预定异步任务连接到单个堆栈跟踪中。Jia Li 将 Zone.js 与异步堆栈标记 API 集成，这使我们能够提供链接的堆栈跟踪。

这两项更改极大地改善了开发人员在 Chrome DevTools 中看到的堆栈跟踪：

```bash

ERROR Error: Uncaught (in promise): Error
Error
    at app.component.ts:18:11
    at fetch (async)
    at (anonymous) (app.component.ts:4)
    at request (app.component.ts:4)
    at (anonymous) (app.component.ts:17)
    at submit (app.component.ts:15)
    at AppComponent_click_3_listener (app.component.html:4)

```

在这里您可以跟踪从 AppComponent 按下按钮一直到出现错误的执行过程。您可以在[此处](https://developer.chrome.com/blog/devtools-modern-web-debugging/)阅读有关改进的更多信息。

## 8. 将基于 MDC 的组件发布到稳定版

我们很高兴地宣布基于 Web material design 组件 (MDC) 的 Angular material 组件的重构现已完成！这一更改使 Angular 能够更接近 Material Design 规范，重用 Material Design 团队开发的原语代码，并使我们能够在最终确定 style token 后采用 Material 3。

对于许多组件，我们更新了样式和 DOM 结构，其他组件我们从头开始重写。我们保留了新组件的大部分 TypeScript API 和组件/指令选择器与旧实现相同。

我们迁移了数千个 Google 项目，这使我们能够使外部迁移路径变得顺畅，并记录所有组件中[更改的完整列表](https://github.com/angular/components/blob/main/guides/v15-mdc-migration.md#comprehensive-list-of-changes)。

由于新的 DOM 和 CSS，您可能会发现应用程序中的某些样式需要调整，特别是当您的 CSS 覆盖任何迁移组件上的内部元素的样式时。

每个新组件的旧实现现已弃用，但仍可通过“legacy”导入使用。例如，您可以 mat-button 通过导入旧按钮模块来导入旧的实现。

```ts
import { MatLegacyButtonModule } from "@angular/material/legacy-button";
```

请访问[迁移指南](https://github.com/angular/components/blob/main/guides/v15-mdc-migration.md#how-to-migrate)以获取更多信息。

我们将许多组件移至后台使用 design token 和 CSS 变量，这将为应用程序采用 Material 3 组件样式提供一条平滑的路径。

## 9. 组件方面的更多改进

我们解决了第四个投票最多的问题——滑块中的范围选择支持。

要获取范围输入，请使用：

```html
<mat-slider>
  <input matSliderStartThumb />
  <input matSliderEndThumb />
</mat-slider>
```

此外，所有组件现在都有一个 API 来自定义 density，这解决了另一个热门的 GitHub 问题。

您现在可以通过自定义主题来指定所有组件的默认 density：

```ts

@use '@angular/material' as mat;

$theme: mat.define-light-theme((
  color: (
    primary: mat.define-palette(mat.$red-palette),
    accent: mat.define-palette(mat.$blue-palette),
  ),
  typography: mat.define-typography-config(),
  density: -2,
));

@include mat.all-component-themes($theme);

```

新版本的组件包括广泛的可访问性改进，包括更好的对比度、增加的触摸目标尺寸和改进的 ARIA 语义。

### 9.1. CDK 列表框

组件开发工具包 (CDK) 提供了一组用于构建 UI 组件的行为原语。在 v15 中，我们引入了另一个可以根据您的用例进行自定义的原语 — CDK 列表框：

该@angular/cdk/listbox 模块提供指令来帮助创建基于 WAI ARIA 列表框模式的自定义列表框交互。

通过使用，@angular/cdk/listbox 您可以获得无障碍体验的所有预期行为，包括双向布局支持、键盘交互和焦点管理。所有指令都将其关联的 ARIA 角色应用于其宿主元素。

## 10. 实验性 esbuild 支持的改进

在 v14 中，我们宣布对 esbuild 提供实验性支持 ng build，以实现更快的构建时间并简化我们的管道。

在 v15 中，我们现在有了实验性的 Sass、SVG 模板、文件替换和 ng build --watch 支持！请通过以下位置更新您的构建器来尝试 esbuild angular.json：

将 browser builder

```json

"builder": "@angular-devkit/build-angular:browser"
```

修改为 browser-esbuild browser

```json
"builder": "@angular-devkit/build-angular:browser-esbuild"
```

## 11. 语言服务中的自动导入

语言服务现在可以自动导入您在模板中使用但尚未添加到独立组件或 NgModule 中的组件。

## 12. CLI 改进

在 Angular CLI 中，我们引入了对 standalone 稳定 API 的支持。现在，您可以通过 ng g 组件生成一个新的独立组件——standalone。
我们还肩负着简化 ng new 输出的使命。作为第一步，我们通过删除 test.ts、polyfills.ts 和 environments 来减少配置。现在，您可以在 polyfills 部分的 angular.json 中直接指定您的 polyfills：

```json

"polyfills": [
  "zone.js"
]

```

为了进一步减少配置开销，我们现在使用.browserlist 来定义目标 ECMAScript 版本。

## 13. 社区贡献亮点

我们很高兴与大家分享，自 v14 发布以来，我们收到了来自框架、组件和 CLI 的 210 多人的贡献！在本节中，我想重点介绍其中的两个。

### 13.1. 提供配置 DatePipe 默认选项的功能

Matthias Weiß 的此功能允许您全局更改 DatePipe 的默认格式配置。下面是新 bootstrapApplication API 的示例：

```ts
bootstrapApplication(AppComponent, {
  providers: [
    {
      provide: DATE_PIPE_DEFAULT_OPTIONS,
      useValue: { dateFormat: "shortDate" },
    },
  ],
});
```

上面的配置将为您在应用程序中使用 DatePipe 的所有位置启用 shortDate 格式。

### 13.2. 在 SSR 期间为优先级图像添加＜ link ＞预加载标签

为了确保尽快加载优先图像，Jay Bell 在图像指令中添加了一项功能，在使用 Angular Universal 时为其添加了\<link rel=“preload”>标签。
如果您已经启用了 image 指令，您则无需在执行任何操作。如果已将图像指定为优先，则指令将自动预加载该图像。

## 14. 弃用

主要版本使我们能够使框架朝着简单、更好的开发人员体验和与 web 平台保持一致的方向发展。

在分析了谷歌内部数千个项目后，我们发现很少有人使用在大多数情况下被滥用的模式。因此，我们反对`providedIn：“any”`是一个选项，除了框架内部的少数深奥案例外，它的用途非常有限。

我们也在弃用`providedIn: NgModule`。它没有广泛的用途，在大多数情况下使用不正确，在您应该更喜欢`providedIn:'root'`的情况下。如果您确实应该将提供程序的范围限定为特定的 NgModule，请改用 NgModule.providers。

随着 CSS 布局的不断发展，团队将停止发布新版本的@angular/flex 布局。我们将在明年继续提供安全和浏览器兼容性修复程序。您可以在我们的“现代 CSS”系列的[第一篇博客文章](https://blog.angular.io/modern-css-in-angular-layouts-4a259dca9127)中了解更多信息。

## 15. 对接下来的事情感到兴奋

Ivy 在 2020 年的推出带来了许多全面的改进，你可以发现这些改进已经在推广。可选 NgModules 就是一个很好的例子。它有助于减少初学者在关键学习过程中需要处理的概念，并通过独立指令支持指令组合 API 等高级功能。

接下来，我们将在服务器端渲染管道和反应性方面进行改进，同时全面提高生活质量！

迫不及待地想与您分享下一步的进展！

## 16. 相关文章

最新更新以及更多 Angular 相关文章请访问 [Angular合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

<!--
## 参考文档

[Angular v15 is now available!](https://blog.angular.io/angular-v15-is-now-available-df7be7f2f4c8)
 -->
