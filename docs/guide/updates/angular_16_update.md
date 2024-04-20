---
title: Angular 16 有哪些更新?
date: 2023/11/06
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

六个月前，我们通过将 standalone API 从开发者预览版中升级到稳定版，在 Angular 的简单性和开发者体验方面达到了一个重要的里程碑。今天，我们很高兴与大家分享，我们将继续保持 Angular 的势头，推出自 Angular 首次推出以来最大规模的版本, 在反应性、服务器端渲染和工具方面取得了巨大飞跃。所有这些都伴随着针对功能请求的数十项用户体验改进，GitHub 上总共有超过 2500 个点赞！

这篇文章包含大量内容，涵盖了我们在过去六个月中所做的大部分改进。

<!-- more -->

## 1. 重新思考反应性

作为 v16 版本的一部分，我们很高兴与大家分享 Angular 全新反应性模型的开发者预览版，该模型显着改进了性能和开发者体验。

它完全向后兼容并与当前系统互操作，并支持：

- 通过减少变更检测期间的计算数量来提高运行时性能。一旦 Angular Signals 完全推出，我们预计使用信号构建的应用程序的[INP](https://web.dev/inp) Core Web Vital 指标将得到显着改进
- 为反应性带来更简单的心智模型，明确视图的依赖关系是什么以及应用程序中的数据流是什么
- 启用细粒度的反应性，在未来的版本中，我们将仅检查受影响组件中的更改
- 通过在模型更改时使用信号通知框架，使 Zone.js 在未来版本中成为可选项
- 提供计算属性，而无需在每个更改检测周期中重新计算
- 通过概述引入反应性输入的计划，实现与 RxJS 更好的互操作性

最初的 GitHub 讨论收到了 682 条评论，此后我们分享了一系列 RFC，又收到了 1000 多条评论！

在 v16 中，您可以找到一个新的信号库，它是@angular/coreRxJS 互操作包的一部分 @angular/core/rxjs-interop 框架中的完整信号集成将于今年晚些时候推出。

## 2. Angular Signals

Angular Signals 库允许您定义反应值并表达它们之间的依赖关系。您可以在[相应的 RFC](https://github.com/angular/angular/discussions/49683)中了解有关该库属性的更多信息。以下是一个如何将它与 Angular 一起使用的简单示例：

```ts
@Component({
  selector: "my-app",
  standalone: true,
  template: `
    {{ fullName() }} <button (click)="setName('John')">Click</button>
  `,
})
export class App {
  firstName = signal("Jane");
  lastName = signal("Doe");
  fullName = computed(() => `${this.firstName()} ${this.lastName()}`);

  constructor() {
    effect(() => console.log("Name changed:", this.fullName()));
  }

  setName(newName: string) {
    this.firstName.set(newName);
  }
}
```

上面的代码片段创建了一个计算值 fullName，该值取决于信号 firstName 和 lastName。我们还声明了一个效果，每次我们更改它读取的任何信号的值时都会执行该回调 - 在本例中 fullName，这意味着它也传递地依赖于 firstName 和 lastName。

当我们将 的值设置 firstName 为“John”时，浏览器将登录到控制台：

```bash

"Name changed: John Doe"

```

### 2.1. RxJS 互操作性

@angular/core/rxjs-interop 作为 v16 版本的一部分，您将能够通过开发者预览版中的函数轻松地将信号“提升”到可观察量！

以下是将信号转换为可观察信号的方法：

```ts

import { toObservable } from '@angular/core/rxjs-interop';

@Component({...})
export class App {
  count = signal(0);
  count$ = toObservable(this.count);

  ngOnInit() {
    this.count$.subscribe(() => ...);
  }
}

```

这是一个如何将可观察量转换为信号以避免使用异步管道的示例：

```ts
import { toSignal } from "@angular/core/rxjs-interop";

@Component({
  template: ` <li *ngFor="let row of data()">{{ row }}</li> `,
})
export class App {
  dataService = inject(DataService);
  data = toSignal(this.dataService.data$, []);
}
```

Angular 用户通常希望在相关主题完成时完成流。以下说明性模式非常常见：

```ts

destroyed$ = new ReplaySubject<void>(1);

data$ = http.get('...').pipe(takeUntil(this.destroyed$));

ngOnDestroy() {
  this.destroyed$.next();
}

```

我们引入了一个名为 takeUntilDestroyed 的新 RxJS 运算符，它将此示例简化为以下内容：

```ts
data$ = http.get("…").pipe(takeUntilDestroyed());
```

默认情况下，此操作符将注入当前清理上下文。例如，在组件中使用时，它将使用组件的生命周期。
当您想将 Observable 的生命周期与特定组件的生命周期联系起来时，takeUntilDestroyed 特别有用。

### 2.2. 下一步我们将会针对信号做些什么?

接下来，我们将研究基于信号的组件，这些组件具有一组简化的生命周期钩子，以及一种更简单的声明式输入和输出的替代方法。我们还将编写一套更完整的示例和文档。
Angular 存储库中最受欢迎的问题之一是“Proposal:Input as Observable”。几个月前，我们回应说，我们希望支持这个用例，作为框架中更大努力的一部分。我们很高兴与大家分享，今年晚些时候，我们将推出一项功能，该功能将启用基于信号的输入——您将能够通过 interop 包将输入转换为可观测值！

## 3. 服务器端渲染和水合

根据我们的年度开发者调查，服务器端渲染是 Angular 改进的首要机会。在过去的几个月里，我们与 Chrome Aurora 团队合作，提高了水合 DX 和服务器端渲染的性能。今天我们很高兴分享全应用无损水合的开发者预览！

在新的完整应用程序无损水合作用中，Angular 不再从头开始重新渲染应用程序。相反，该框架在构建内部数据结构时查找现有的 DOM 节点，并将事件侦听器附加到这些节点。

好处是：

- 对于最终用户来说，页面上没有内容闪烁
- 在某些情况下更好的 Web Core Vitals
- 面向未来的架构，支持使用我们将于今年晚些时候发布的原语进行细粒度代码加载。目前，这在渐进式懒惰路线补水中表现出来
- 只需几行代码即可轻松与现有应用程序集成（请参阅下面的代码片段）
- ngSkipHydration 对于执行手动 DOM 操作的组件，逐步采用模板中的属性来实现水合

在早期测试中，我们发现在应用程序完全水合作用的情况下， Largest Contentful Paint 的性能提升高达 45% ！

一些应用程序已经在生产中启用了水合作用，并报告了 CWV 的改进.

要开始它就像在您的中添加几行一样简单 main.ts：

```json

import {
  bootstrapApplication,
  provideClientHydration,
} from '@angular/platform-browser';

...

bootstrapApplication(RootCmp, {
  providers: [provideClientHydration()]
});

```

您可以在[文档](https://angular.io/guide/hydration)中找到有关其工作原理的更多详细信息。

### 3.1. 新的服务器端渲染功能

作为 v16 版本的一部分，我们还更新了 Angular Universal 的 [ng add schematics](https://github.com/angular/universal/commit/3af1451abac574f5e57c5f8b45192532bec2b23a)，使您能够使用 standalone API 将服务器端渲染添加到项目中。我们还引入了对内联样式更严格的内容安全策略的支持。

### 3.2. 水合和服务器端渲染的后续步骤

我们计划在这里做更多的事情，v16 中的工作只是一个垫脚石。在某些情况下，有机会延迟加载对于页面来说并不重要的 JavaScript，并在稍后水合相关组件。这种技术称为部分水合，我们接下来将对其进行探讨。

自从 Qwik 从 Google 的闭源框架 Wiz 中推广了可恢复性的想法以来，我们收到了许多对 Angular 中的此功能的请求。可恢复性肯定是我们关注的焦点，我们正在与 Wiz 团队密切合作来探索这一领域。我们对它所带来的开发人员体验限制持谨慎态度，评估不同的权衡，并在我们取得进展时随时向您通报。

您可以在[“ Angular 中服务器端渲染的下一步是什么”](https://blog.angular.io/whats-next-for-server-side-rendering-in-angular-2a6f27662b67)中了解有关我们未来计划的更多信息。

## 4. 改进了独立组件、指令和管道的工具

Angular 是数百万开发人员用于许多关键任务应用程序的框架，我们认真对待重大更改。我们几年前就开始探索 standalone API，2022 年我们在开发者预览下发布了它们。现在，经过一年多的收集反馈和对 API 的迭代，我们希望鼓励更广泛的采用！

为了支持开发人员将其应用程序转换为独立 API，我们开发了迁移 schematics 和 standalone 迁移指南。一旦进入项目目录，请运行：

```bash
ng generate@angular/core:standalone
```

这个 schematic 将转换您的代码，删除不必要的 NgModules 类，并最终将项目的引导程序更改为使用独立的 API。

## 5. 可以使用 standalone 选项创建项目

作为 Angular v16 的一部分，您可以从一开始使用 standalone 选项创建新项目！要尝试 standalone schematics 的开发者预览版，请确保您使用的是 Angular CLI v16 并运行：

```bash
ng new --standalone
```

您将获得更简单的项目结构，无需任何 NgModules. 此外，项目中的所有生成器都将生成独立的指令、组件和管道！

## 6. 配置 Zone.js

在 standalone API 首次发布后，我们从开发人员那里得知, 您希望能够使用新 bootstrapApplication API 配置 Zone.js。

我们为此添加了一个选项 provideZoneChangeDetection：

```ts
bootstrapApplication(App, {
  providers: [provideZoneChangeDetection({ eventCoalescing: true })],
});
```

## 7. 加强开发人员工具

现在，让我们分享 Angular CLI 和语言服务(language service)的一些功能亮点。

### 7.1. 基于 esbuild 构建系统的开发者预览版

一年前，我们宣布我们正在 Angular CLI 中对 esbuild 进行实验性支持，以使您的构建速度更快。今天，我们很高兴与大家分享，我们基于 esbuild 的构建系统在 v16 中进入了开发者预览版！早期测试显示 cold production 构建提高了 72% 以上。

我们 ng serve 现在使用 Vite 作为开发服务器，esbuild 既为开发环境也为生产环境构建提供支持！

我们想强调的是，Angular CLI 完全依赖 Vite 作为开发服务器。为了支持选择器匹配，Angular 编译器需要维护组件之间的依赖关系图，这需要与 Vite 不同的编译模型。

您可以通过更新 angular.json 以下内容来尝试 Vite + esbuild

```json
...
"architect" :  {
  "build" :  {                      /* 添加 esbuild 后缀 */
    "builder" :  "@angular-devkit/build-angular:browser-esbuild" ,
 ...

```

接下来，我们将在该项目退出开发者预览版之前解决对 i18n 的支持问题。

## 8. 使用 Jest 和 Web Test Runner 更好地进行单元测试

根据 Angular 和更广泛的 JavaScript 社区的开发人员调查，Jest 是最受欢迎的测试框架和测试运行程序之一。我们收到了大量支持 Jest 的请求，由于不需要真正的浏览器，因此降低了复杂性。

今天，我们很高兴地宣布我们将推出实验性 Jest 支持。在未来的版本中，我们还将把现有的 Karma 项目移至 Web Test Runner，以继续支持基于浏览器的单元测试。这对于大多数开发人员来说不需要做什么事情。

`npm install jest --save-dev`您可以通过安装 Jest 并更新 angular.json 文件来在新项目中试验 Jest：

```json
{
  "projects": {
    "my-app": {
      "architect": {
        "test": {
          "builder": "@angular-devkit/build-angular:jest",
          "options": {
            "tsConfig": "tsconfig.spec.json",
            "polyfills": ["zone.js", "zone.js/testing"]
          }
        }
      }
    }
  }
}
```

您可以在我们[最近的博客文章](https://blog.angular.io/moving-angular-cli-to-jest-and-web-test-runner-ef85ef69ceca)中了解有关我们未来的单元测试策略的更多信息。

## 9. 模板中的自动完成导入

您有多少次在模板中使用组件或管道从 CLI 或语言服务中收到错误，表明您实际上没有导入相应的实现？

Language service 插件现在允许自动导入组件和管道了。

## 10. 还有更多

在 v16 中，我们还启用了对 TypeScript 5.0 的支持，支持 ECMAScript 装饰器，消除了 ngcc 的开销，在 standalone 应用程序中添加了对 Service Worker 和 app shell 的支持，扩展了 CLI 中的 CSP 支持等等！

## 11. 改善开发者体验

除了我们关注的大型计划之外，我们还致力于带来开发者强烈要求的功能。

### 11.1. Required inputs

自从我们在 2016 年引入 Angular 以来，如果不为特定 input 指定值，就不可能出现编译时错误。由于 Angular 编译器在构建时执行检查，因此该更改在运行时增加了开销。多年来，开发人员一直 要求 此功能，我们得到了强烈的迹象表明 这个需求的实现将为开发者带来非常大的方便！

在 v16 中，现在您可以根据需要标记 Required inputs：

```ts
@Component(...)
export class App {
  @Input({ required: true }) title: string = '';
}
```

### 11.2. 将路由数据作为组件输入传递

路由的开发者体验一直在快速进步。GitHub 上的一个流行功能请求是要求能够将路由参数绑定到相应组件的输入。我们很高兴与大家分享，此功能现已作为 v16 版本的一部分提供！

现在您可以将以下数据传递到路由组件的 input 属性：

- 路由数据 —— 解析器和数据属性
- 路径参数
- 查询参数

以下是如何从路由解析访问数据的示例：

```ts

const routes = [
  {
    path: 'about',
    loadComponent: import('./about'),
    resolve: { contact: () => getContact() }
  }
];

@Component(...)
export class About {
  // The value of "contact" is passed to the contact input
  @Input() contact?: string;
}

```

您可以通过使用 withComponentInputBinding 作为 provideRouter 的一部分来启用此功能。

## 12. CSP 对内联样式的支持

Angular 在组件样式的 DOM 中包含的内联样式元素违反了默认的 style-src 内容安全策略 (CSP)。要解决此问题，它们应该包含一个 nonce 属性，或者服务器应该在 CSP 标头中包含样式内容的哈希值。尽管在 Google 我们没有找到针对此漏洞的有意义的攻击向量，但许多公司都执行严格的 CSP，导致 Angular 存储库上的功能请求流行起来。

nonce 在 Angular v16 中，我们实现了一项涵盖框架、Universal、CDK、Material 和 CLI 的新功能，它允许您为 Angular 内联的组件的样式指定属性。有两种方法可以指定随机数：使用属性 ngCspNonce 或通过 CSP_NONCE 注入令牌。

ngCspNonce 如果您有权访问服务器端模板，该模板可以将两者添加 nonce 到标头并 index.html 在构造响应时添加，则该属性非常有用。

```html
<html>
  <body>
    <app ngCspNonce="{% nonce %}"></app>
  </body>
</html>
```

指定随机数的另一种方法是通过 CSP_NONCE 注入令牌。如果您有权在运行时访问 nonce 并且希望能够缓存以下内容，请使用此方法 index.html：

```ts
import { bootstrapApplication, CSP_NONCE } from "@angular/core";
import { AppComponent } from "./app/app.component";

bootstrapApplication(AppComponent, {
  providers: [
    {
      provide: CSP_NONCE,
      useValue: globalThis.myRandomNonceValue,
    },
  ],
});
```

## 13. 灵活的 ngOnDestroy

Angular 的生命周期钩子提供了强大的功能来插入应用程序执行的不同时刻。多年来的一个机会是实现更高的灵活性，例如，提供对 OnDestroy 作为 observable 的访问。

在 v16 中，我们使 OnDestroy 可注入，从而实现了开发人员一直要求的灵活性。这个新功能允许您注入 DestroyRef 对应的组件、指令、服务或管道 - 并注册 onDestroy 生命周期挂钩。可以 DestroyRef 在注入上下文中的任何位置注入，包括组件外部——在这种情况下，onDestroy 当相应的注入器被销毁时，钩子就会被执行：

```ts

import { Injectable, DestroyRef } from '@angular/core';

@Injectable(...)
export class AppService {
  destroyRef = inject(DestroyRef);

  destroy() {
    this.destroyRef.onDestroy(() => /* cleanup */ );
  }
}

```

## 14. 自闭合标签

我们最近实现的一项备受期待的功能允许您在 Angular 模板中的组件中使用自闭合标签。这是一个小的开发人员体验改进，可以帮助您节省一些打字时间！

现在您可以替换：

```html
<super-duper-long-component-name [ prop ]="someVar">
</super-duper-long-component-name>
```

有了这个：

```html
<super-duper-long-component-name [ prop ]="someVar" />
```

## 15. 更好、更灵活的组件

在过去的几个季度中，我们与 Google 的 Material Design 团队密切合作，为使用 Angular Material 的 Web 提供参考 Material 3 实现。我们于 2022 年发布的基于 Web 的 MDC 组件为这项工作奠定了基础。

下一步，我们正努力在今年晚些时候推出一个富有表现力的基于 Token 的主题 API，以实现 Angular 材质组件的更高程度的定制。

提醒您，我们将在 v17 中删除旧的、基于非 MDC 的组件。请务必遵循我们的迁移指南以迁移到最新版本。

## 16. 继续我们的无障碍倡议

遵循 Google 的使命，Angular 让您可以为每个人构建 Web 应用程序！这就是为什么我们不断投资以提高 Angular CDK 和 Material 组件的可访问性。

## 17. 社区贡献亮点

我们想要强调的社区引入的两个功能是：

- Matthieu Riegler 正确使用 ngSkipHydration 的扩展诊断
- Julien Saguet 引入 provideServiceWorker, 使得使用 Service Worker 无需使用 NgModules.

超过 175 人在 GitHub 上为 v16 做出了贡献，还有数千人通过博客文章、演讲、播客、视频、对反应性 RFC 的评论等做出了贡献。

我们要向所有帮助我们使这个版本变得特别的人表示衷心的感谢。

## 18. 让我们一起保持前进的动力

Angular 16 是明年 Angular 反应性和服务器端渲染未来改进的垫脚石。我们将通过开发人员体验和性能方面的创新来推动 Web 向前发展，同时使您能够为每个人进行构建！

您可以成为 Angular Momentum 的一部分，并通过在即将发布的 RFC、调查或社交媒体中分享您的想法来帮助我们塑造框架的未来。

感谢您成为 Angular 社区的一员。我们迫不及待地想让您尝试这些功能！❤️

## 19. 相关文章

最新更新以及更多 Angular 相关文章请访问 [Angular 专题 | 鹏叔的技术博客](https://www.pengtech.net/angular/)


## 20. 参考文档

[Angular v16 is here!](https://blog.angular.io/angular-v16-is-here-4d7a28ec680d)
