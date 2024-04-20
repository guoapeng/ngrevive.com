---
title: Angular 17 有哪些更新?
date: 2023/11/06
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

上个月是 Angular 红盾诞生 13 周年。AngularJS 是新一波 JavaScript 框架的起点，旨在支持对丰富 Web 体验日益增长的需求。今天，我们凭借新的外观和一系列前瞻性功能，通过版本 17 带领大家走向未来，为性能和开发人员体验设定了新标准。

<!-- more -->

![Angular red shield](https://www.pengtech.net/images/angular17/Angular17_logo.png)

在 v17 中，我们很高兴地介绍：

- 可延迟视图(Deferrable views)将性能和开发人员体验提升到一个新的水平
- 在公共基准测试中，通过内置控制流循环，运行时间提高了 90%
- 混合渲染的构建速度提高了 87%，客户端渲染的构建速度提高了 67%
- 全新的外观反映了 Angular 的未来特征
- 全新的互动学习之旅
- 以及许多其他功能和改进！

## 1. 面向未来的品牌形象

在过去的几个版本中，Angular 的复兴一直在全力推进中。我们一直在通过 signal-based 反应性、水合(hydration)、独立组件(standalone components)、指令组合和许多其他功能等改进来加快势头。尽管 Angular 发展迅速，但它的品牌却未能跟上—从 AngularJS 早期以来，它几乎一模一样。

今天，您喜爱的、经过数百万开发者考验的框架焕然一新，反映了其面向未来的开发者体验和性能！

![Angular red shield](https://www.pengtech.net/images/angular17/angular_new_branding.bin)

## 2. 面向未来的文档

与新品牌一起，我们还为 Angular 文档开发了一个新网站 — angular.dev。对于新的文档网站，我们采用了新的结构、新的指南、改进的内容，并构建了一个交互式学习之旅平台，让您可以直接在浏览器中按照自己的节奏学习 Angular 和 Angular CLI。

新的交互式学习体验由 WebContainers 提供支持，让您可以在任何现代 Web 浏览器中使用 Angular CLI 的强大功能！

![Angular dev overview](https://www.pengtech.net/images/angular17/angular_dev_overview.bin)

今天，我们将推出 angular.dev 的 Beta 预览版，并计划将其设为 v18 中 Angular 的默认网站。您可以在“宣布 angular.dev”中了解有关 Angular 新外观和[angular.dev 的更多信息](https://blog.angular.io/announcing-angular-dev-1e1205fa3039)。”

现在让我深入了解 v17 的功能，我们迫不及待地想告诉您！

## 3. 内置控制流程

为了改善开发人员体验，我们发布了新的块模板语法，通过简单的声明性 API 为您提供强大的功能。在底层，Angular 编译器将语法转换为高效的 JavaScript 指令，可以执行控制流、延迟加载等。

我们使用新的块语法来实现优化的内置控制流。在进行用户研究后，我们发现许多开发人员都在为\*ngIf、\*ngSwitch 和 \*ngFor 而苦苦挣扎。自 2016 年开始使用 Angular 并在过去 5 年里成为 Angular 团队的一员，我个人仍然需要查找\*ngFor 和的语法 trackBy。在收集了社区、合作伙伴的反馈并进行了用户体验研究之后，我们为 Angular 开发了一个新的内置控制流程！

内置控制流程可以：

- 更符合人体工程学的语法，更接近 JavaScript，因此更直观，减少帮助文档的查找

- 得益于更优化的类型收敛（type narrowing），类型检查得到了很好的改善

- 内置控制流是构建阶段核心处理的部分，除了大大减少运行时的消耗（甚至直接消失）之外，还将使你的应用程序包大小整体减少 30KB 之多，从而进一步提高应用的核心网络指标（Core Web Vital）的得分

- 无需额外导入，即可在模版中通过变量来使用

- 我们稍后会介绍显着的性能改进

### 3.1. 条件语句

让我们看一下内置控制流中的 if 与 \*ngIf 的 side by side 比较

```html
<div *ngIf="loggedIn; else anonymousUser">The user is logged in</div>
<ng-template #anonymousUser> The user is not logged in </ng-template>
```

使用内置控制流 if 语句，此条件将如下所示：

```js

@if (loggedIn) {
  The user is logged in
} @else {
  The user is not logged in
}

```

@else 与传统的 else 子句相比，能够直接提供内容是对 ngIf 一个重大的简化。当前的控制流也使得拥有@else if 条件语句变得轻而易举，这在历史版本中几乎是是不可能的。

ngSwitch 人体工学改进的更加明显：

```html
<div [ngSwitch]="accessLevel">
  <admin-dashboard *ngSwitchCase="admin" />
  <moderator-dashboard *ngSwitchCase="moderator" />
  <user-dashboard *ngSwitchDefault />
</div>
```

通过内置控制流程，它变成：

```js

@switch (accessLevel) {   @case ( 'admin' )   { <admin-dashboard/> } @case (   ' moderator ' ) { <moderator-dashboard/> } @default { < user -dashboard/> } }

```

新的控制流可以在各个流程分支中更好地缩小类型，使用 ngSwitch 是做不到这一点的.

### 3.2. for 循环

我最喜欢的更新之一是我们引入的内置 for 循环，它除了开发人员体验改进之外，还将 Angular 的渲染速度推向了另一个水平！

其基本语法是：

```js
@for (user of users; track user.id) {
  {{ user.name }}
} @empty {
  Empty list of users
}
```

当使用 ngFor 时我们经常看到应用程序由于缺乏 trackBy 功能而出现性能问题。一些区别是@for，track 是强制性的，以确保快速比较性能。此外，它更容易使用，因为它只是一个表达式而不是组件类中的方法。内置@for 循环还具有通过 optional @empty 快捷处理空集合。

@for 语句使用了新的 diffing 算法，并且与 ngFor 相比具有更优化的实现，这使得社区框架基准测试的运行时间提高了 90% ！

### 3.3. 尝试内置控制流

内置控制流现已在 v17 的开发者预览版中提供！

内置控制流的设计目标之一是实现完全自动化的迁移。要在现有项目中尝试它，请使用以下迁移：

```bash
ng generate @angular/core:control-flow
```

### 3.4. 接下来我们将会做些什么

您已经可以使用带有最新语言服务的内置控制流，我们与 JetBrains 密切合作，以便在他们的产品中提供更好的支持。我们还与 Prettier 的 Sosuke Suzuki 联系，以确保 Angular 模板的格式正确。

ngIf 与、ngFor、ngSwitch 和相比内置控制流处理 content projection 的方式仍然存在一些差异，我们将在接下来的几个月内解决这些问题。除此之外，我们对内置控制流的实现和稳定性充满信心，所以您今天就可以尝试一下！我们希望将其保留在开发者预览版中，直到下一个主要版本，以便我们可以为潜在的向后不兼容修复打开大门，以便我们找到更多进一步增强开发者体验的机会.

## 4. 可延迟视图(Deferrable views)

现在让我们谈谈延迟加载的未来！利用新的块语法，我们开发了一种新的强大机制，您可以使用它来使您的应用程序更快。在博客文章的开头，我说过可延迟视图将性能和开发人员体验提升到了一个新的水平，因为它们通过前所未有的人体工程学实现了声明性和强大的延迟加载(deferred loading)。

![deferred loading](https://www.pengtech.net/images/angular17/deferred_loading.webp)

假设您有一个博客，并且您想延迟加载用户评论列表。目前，您必须在使用的 ViewContainerRef 同时管理清理的所有复杂性、管理加载错误、显示占位符等。处理各种极端情况可能会导致一些复杂度高极高的代码，这将难以测试和调试。

新的可延迟视图允许您使用一行声明性代码延迟加载注释列表及其所有传递依赖项：

```ts
@defer {
  <comment-list />
}
```

当某个 DOM 元素进入视口时开始延迟加载组件涉及许多更重要的逻辑和 IntersectionObserver API。Angular 使 IntersectionObservers 的使用变得简单到只需要添加可延迟视图触发器即可！

```ts
@defer (on viewport) {
  <comment-list />
} @placeholder {
  <!-- A placeholder content to show until the comments load -->
  <img src="comments-placeholder.png">
}
```

在上面的示例中，Angular 首先渲染占位符块的内容。当它在视口中可见时，组件就会开始加载<comment-list/>。加载完成后，Angular 会删除占位符并渲染组件。

还有用于加载和错误状态的块：

```ts

@defer (on viewport) {
  <comment-list/>
} @loading {
  Loading…
} @error {
  Loading failed :(
} @placeholder {
  <img src="comments-placeholder.png">
}

```

就是这样！Angular 为您管理了大量的复杂性。

可延迟视图提供了更多触发器：

- on idle- 当浏览器不做任何繁重的工作时延迟加载块
- on immediate— 自动开始延迟加载，不阻塞浏览器
- on timer(<time>)— 使用计时器延迟加载
- on viewport 并且 on viewport(<ref>)- 视口还允许指定锚元素的引用。当锚元素可见时，Angular 将延迟加载组件并渲染它
- on interaction 并且 on interaction(<ref>)- 使您能够在用户与特定元素交互时启动延迟加载
- on hoverand on hover(<ref>)- 当用户悬停元素时触发延迟加载
- when <expr>— 使您能够通过布尔表达式指定您自己的条件

可延迟视图还提供了在渲染依赖项之前预取依赖项的能力。添加预取就像 prefetch 向 defer 块添加语句一样简单，并且支持所有相同的触发器。

```ts
@defer (on viewport; prefetch on idle) {
  <comment-list />
}
```

今天，可延迟视图在 v17 的开发者预览版中可用！[[了解有关本指南](https://angular.io/guide/defer)中该功能的更多信息。

### 4.1. 下一步可延迟视图将会如何发展？

可延迟视图已准备好使用，我们强烈鼓励您尝试一下！我们将它们保留在开发人员预览中的原因是这样我们可以收集更多反馈并在 API 表面中引入更改，直到我们将它们锁定为像框架的其余部分一样遵循语义版本控制。

目前，服务器端渲染将渲染指定的占位符。一旦框架加载应用程序并对其进行水合，可延迟视图将按照我们上面描述的方式工作。

下一步，我们将探索在服务器上渲染延迟块内的内容，并在客户端上启用部分水合作用。在这种情况下，客户端不会下载延迟视图的代码，直到触发器请求它。此时，Angular 将下载相关的 JavaScript 并仅对视图的这一部分进行水合。

还将有许多令人兴奋的信号互操作性，敬请期待！

## 5. 改进的混合渲染体验

今天，我们通过以下一行提示让开发人员开启服务器端渲染 (SSR) 和静态站点生成（SSG 或预渲染）

```bash

ng new

```

![ng new enable ssr and ssg](https://www.pengtech.net/images/angular17/ng_new_enable_ssr_ssg.bin)

这是我们长期以来一直想要做出的改变，但首先我们希望对 Angular 的 SSR 开发人员体验充满信心。

或者，您可以通过以下方式在新项目中启用 SSR：

```bash
ng new my-app --ssr
```

## 6. Hydration 从开发者预览版毕业

在过去的 6 个月里，我们看到数千个应用程序采用了水合作用。今天，我们很高兴地宣布，水合作用已不再是开发者预览版，并且在所有使用服务器端渲染的新应用程序中默认启用！

新的 @angular/ssr 包
我们将 Angular 通用存储库移至 Angular CLI 存储库，并使服务器端渲染成为我们工具产品中更不可或缺的一部分！

从今天开始，要向现有应用程序添加混合渲染支持，请运行：

```bash

ng add @angular/ssr

```

此命令将生成服务器入口点，添加 SSR 和 SSG 构建功能，并默认启用水合。@angular/ssr 提供与@nguniversal/express-engine 当前处于维护模式的功能等效的功能。如果您使用的是 express-engine，Angular CLI 会自动将您的代码更新为@angular/ssr.

从旧平台迁移到最新的 Angular 混合渲染解决方案后，Virgin Media O2 的销售额增长了 112%。NgOptimizedImage 通过与 Angular SSR 和 DOM Hydration 结合使用，累积布局偏移平均减少了 99.4% 。

## 7. 使用 SSR 部署您的应用程序

为了进一步增强开发人员体验，我们与云提供商密切合作，以实现顺利部署到他们的平台。

Firebase 现在将通过其新的框架感知 CLI 的早期预览版，以接近零的配置自动识别和部署您的 Angular 应用程序。

```bash

firebase experiments:enable webframeworks
firebase init hosting
firebase deploy

```

框架感知(framework-aware)的 CLI 可识别 SSR、i18n、图像优化等的使用，使您能够在经济高效的无服务器基础设施上提供高性能的 Web 应用程序。

对于那些拥有复杂 Angular monorepos 或只是喜欢本机工具的人，AngularFire 允许使用以下方式部署到 Firebase ng deploy：

```bash

ng add @angular/fire
ng deploy

```

为了能够部署到边缘工作人员，我们在 Angular 的服务器端渲染中启用了 ECMAScript 模块支持，引入了 fetch 后端 HttpClient，并与 CloudFlare 合作来简化流程。

## 8. 新的生命周期钩子

为了提高 Angular 的 SSR 和 SSG 的性能，从长远来看，我们希望摆脱 DOM 模拟和直接 DOM 操作。同时，在大多数应用程序的生命周期中，它们需要与元素交互以实例化第三方库、测量元素大小等。

为了实现这一点，我们开发了一组新的生命周期挂钩：

- afterRender— 注册每次应用程序完成渲染时调用的回调
- afterNextRender— 注册一个回调，以便在下次应用程序完成渲染时调用

只有浏览器才会调用这些钩子，这使您能够将自定义 DOM 逻辑安全地直接插入组件中。例如，如果您想实例化一个图表库，您可以使用

```ts
@Component({
  selector: 'my-chart-cmp',
  template: `<div #chart>{{ ... }}</div>`
})
export class MyChartCmp {
  @ViewChild('chart') chartRef: ElementRef;
  chart: MyChart | null;

  constructor() {
    afterNextRender(
      () => {
        this.chart = new MyChart(this.chartRef.nativeElement);
      },
      { phase: AfterRenderPhase.Write }
    );
  }
}
```

每个钩子都支持一个阶段值（例如读、写），Angular 将使用该阶段值来安排回调以减少布局抖动并提高性能。

## 9. 新项目默认使用 Vite 和 esbuild

![Angular red shield](https://www.pengtech.net/images/angular17/Vite_and_esbuild.webp)

如果没有对 Angular CLI 的构建管道进行根本性的改变，我们从一开始就无法在 Angular 中启用 SSR！

在 v16 中，我们引入了 esbuild 和 Vite 支持的构建体验的开发者预览版。从那时起，许多开发人员和一些企业合作伙伴都尝试了它，报告称他们的一些应用程序的构建时间缩短了 67% ！今天，我们很高兴地宣布，新的应用程序构建器已从开发者预览版中毕业，并且默认为所有新应用程序启用！

此外，我们还更新了使用混合渲染时的构建管道。借助 SSR 和 SSG，您可以观察到 ng build 的速度提高了 87%，ng serve 时修改刷新 loop 速度提高了 80%。

![Angular red shield](https://www.pengtech.net/images/angular17/compare_ng_build_pipeline.webp)

在未来的次要版本中，我们将提供 schematics，以使用混合渲染（​​ 使用 SSG 或 SSR 进行客户端渲染）自动迁移现有项目。如果您今天想测试新的应用程序构建器，请查看我们[文档中的指南](https://angular.io/guide/esbuild)。

## 10. DevTools 中的依赖注入调试

去年，我们展示了 Angular DevTools 中依赖注入调试功能的预览。在过去的几个月里，我们实现了全新的调试 API，使我们能够插入框架的运行时并检查注入器树。

基于这些 API，我们构建了一个检查用户界面，允许您预览：

- 组件检查器中组件的依赖关系
- 注入器树和依赖解析路径
- 在各个注入器中声明的 Providers

您可以在下面的动画中快速预览这些功能。在 angular.io 上了解有关[Angular DevTools 的更多信息](https://angular.io/guide/devtools)。

![Angular red shield](https://www.pengtech.net/images/angular17/angular_devtools.bin)

下一步，我们将完善 UI 并致力于更好地可视化注入器层次结构、providers 及其分辨率。

## 11. 从项目创建时就使用 Standalone API

在过去一年半的时间里收集了独立组件、指令和管道的反馈并完善了它们的 DevEx 后，我们有信心从一开始就在所有新应用程序中启用它们。所有 ng generate 命令现在都将构建独立组件、指令和管道。

与此同时，我们还重新审视了 Angular.io 和 Angular.dev 的整个文档，以确保一致的学习体验、开发实践和建议。

在可预见的将来，我们将保留 NgModules，但看到新的独立 API 的好处，我们强烈建议您逐步将项目迁移到它们。我们还提供了一个示意图，可以为您自动完成大部分工作：

```bash
ng generate @angular/core:standalone
```

有关更多信息，请查看我们的[迁移指南](https://angular.io/guide/standalone-migration)。

## 12. reactivity 的后续计划

Angular 新的基于信号的 reactive 系统是我们在该框架中所做的最大转变之一。为了确保与基于 Zone.js 的变更检测的向后兼容性和互操作性，我们一直在努力制作原型并设计前进的道路。

今天，我们很高兴地宣布 Angular Signals 实现已通过开发者预览版。目前，我们将将该 effect 函数保留在开发人员预览状态下，以便我们可以进一步迭代其语义。

在接下来的几个月中，我们将开始推出基于信号的输入、视图查询等功能。到明年 5 月，在 Angular v18 中，我们将提供许多功能来进一步改善开发人员使用 Signals 的体验。

## 13. testing 的后续计划

我们将继续试验 Jest，并确保我们构建一个高性能、灵活且直观的解决方案，足以满足开发人员的需求。我们还开始尝试 Web Test Runner，并为初始实施提供了一个开放的 PR 。在不久的将来，我们可能会首先关注 Web Test Runner，以解锁那些渴望摆脱 Karma 的项目。

## 14. Material 3 的后续计划

我们一直在与 Google 的 Material Design 团队努力合作，重构 Angular Material 的内部结构，以纳入 Design token，该系统将为组件提供更多的自定义选项并启用 Material 3 支持。虽然我们还没有准备好为 v17 提供设计令牌和 M3 支持，但我们预计很快会在 v17 小版本中提供这些功能。

在 2022 年第四季度，我们宣布推出基于 MDC 的新 Angular Material 组件，并弃用具有相同功能但 DOM 结构和样式不同的旧组件。我们在 v15 中弃用了旧组件，并将在 v17 中删除。即使它们不属于 Angular Material v17 包的一部分，您仍然可以将应用程序更新到 Angular v17 并使用 v16 Angular Material 包。在 v18 之前，这将是一个选项，之后 Angular Material v16 将不再与较新版本的 Angular 兼容。我们还与 HeroDevs 的合作伙伴合作，他们将提供无休止的付费支持，以防您暂时无法执行迁移。

## 15. 开发体验改善

除了所有这些面向未来的功能之外，我们还从待办事项中提供了一系列较小的开发人员体验增强功能！

## 16. 实验性视图转换支持

[视图转换 API](https://developer.chrome.com/docs/web-platform/view-transitions/)可在更改 DOM 时实现平滑转换。在 Angular 路由器中，我们现在通过该 withViewTransitions 功能提供对此 API 的直接支持。使用此功能，您可以使用浏览器的本机功能在路线之间创建动画过渡。

您现在可以通过在引导期间在路由器的提供程序声明中配置此功能来将此功能添加到您的应用程序中：

```ts
bootstrapApplication(App, {
  providers: [provideRouter(routes, withViewTransitions())]
});
```

withViewTransitions 接受带有 property 的可选配置对象 onViewTransitionCreated，这是一个为您提供一些额外控制的回调：

- 决定是否要跳过特定动画
- 向文档添加类以自定义动画并在动画完成时删除这些类
- 等等。

## 17. 图像指令中的自动预连接

Angular 图像指令现在会自动为您作为参数提供给图像加载器的域生成预连接链接。如果图像指令无法自动识别源并且未检测到 LCP 图像的预连接链接，它将在开发过程中发出警告。

在[图像指令指南](https://angular.io/guide/image-directive)中了解有关此功能的更多信息。

## 18. 延迟加载动画模块

此功能可以使您的初始捆绑包（压缩后的 16KB）减少 60KB。社区贡献者 Matthieu Riegler 提出并实现了一项功能，允许您通过异步提供程序函数延迟加载动画模块：

```ts
import { provideAnimationsAsync } from '@angular/platform-browser/animations-async';

bootstrapApplication(RootCmp, {
  providers: [provideAnimationsAsync()]
});
```

## 19. 输入值转换

常见的模式是具有接收布尔输入的组件。然而，这对如何将值传递给此类组件设置了限制。例如，如果我们对 Expander 组件有以下定义：

```ts
@Component({
  standalone: true,
  selector: 'my-expander',
  template: `…`
})
export class Expander {
  @Input() expanded: boolean = false;
}
```

...我们尝试将其用作：

```ts
<my-expander expanded />
```

您将收到“字符串不可分配给布尔值”的错误。输入值转换允许您通过配置输入装饰器来解决此问题：

```ts
@Component({
  standalone: true,
  selector: 'my-expander',
  template: `…`
})
export class Expander {
  @Input({ transform: booleanAttribute }) expanded: boolean = false;
}
```

您可以在 GitHub 上找到原始功能请求 -[布尔属性作为 HTML 二进制属性](https://github.com/angular/angular/issues/14761)。

## 20. 作为字符串的 Style 和 styleUrls

Angular 组件支持每个组件多个样式表。然而，绝大多数情况下，当我想要设置组件的样式时，我会创建一个数组，其中包含指向内联样式或引用外部样式表的单个元素。一项新功能使您可以切换：

```ts

@Component({
  styles: [`
    ...
  `]
})

```

```ts

@Component({
  styleUrls: ['styles.css']
})

```

切换到更简单、更符合逻辑的形式：

```ts

@Component({
  styles: `
    ...
  `
})

```

```ts

@Component({
  styleUrl: 'styles.css'
})

```

当您使用数组时，我们仍然支持多个样式表。这更符合人体工程学，更直观，并且与自动格式化工具配合使用效果更好。

## 21. 社区 schematics

为了支持社区 schematics 的开发，我们提供了一些实用方法作为@schematics/angular/utility. 现在，您可以将表达式直接导入到 Angular 应用程序的根目录中，并将 providers 添加到 Angular 应用程序的 package.json 文件.

您可以在文档中的 schematics 指南中了解更多信息。

## 22. Angular 开发人员培训

我们与 EdTech 交互式平台 SoloLearn 合作，基于我们最近开发的“ Angular 简介”课程开发了新的 Angular 培训。他们创建了一个互动学习之旅，在过去两个月内覆盖了超过 7 万人！

请参阅我们[最近的公告](https://blog.angular.io/new-free-interactive-angular-course-for-beginners-on-sololearn-7a4c4f91810a)了解更多信息

## 23. 社区亮点

我们要感谢 346 位贡献者，是他们让 Angular v17 变得如此特别！我们想列出一些亮点：

- HttpClient 现在可以使用 fetch 作为后端，这是使 Angular 能够在边缘工作线程中运行的功能之一。我们要感谢 Matthieu Riegler 的帮助

- Matthieu 还启用了自定义功能，HttpTransferCache 允许对发布请求指定标头、过滤器和缓存

- Cédric Exbrayat 在新的应用程序构建器中引入了支持 namedChunks

- Thomas Laforge 的 Angular Challenges 是一个优秀的资源网站，它一直在帮助 Angular 开发人员达到新的水平

- [AnalogJS](https://analogjs.org/)一直在稳步发展并接近 1.0。祝贺布兰登·罗伯茨所做的出色工作！

- 祝贺 Santosh Yadav 的[Angular 初学者课程](https://www.youtube.com/watch?v=3qBXWUpoPHo)浏览量达到 100 万次

## 24. 用 Angular 构建未来

在过去的六个月里，我们一直在继续 Angular 的复兴，发布了一些功能，以提供更好的开发人员体验和性能。今天，我们很高兴在 Angular 更新的品牌和 angular.dev 的学习体验中体现出这种势头。

在下一个发布周期中，预计 Angular 基于信号的反应性、混合渲染和学习之旅将发生大量演变。

我们很荣幸能够成为您使用 Angular 构建未来的旅程的一部分！谢谢你！

## 25. 参考文档

[Introducing Angular v17](https://medium.com/angular-blog/introducing-angular-v17-4d7033312e4b)
