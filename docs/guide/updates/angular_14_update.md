---
title: Angular 14 有哪些更新？
order: 100
---

我们很高兴地宣布 Angular v14 发布！从类型化表单和独立组件到 Angular CDK（组件开发工具包）中的新原语，我们很高兴分享每个功能如何使 Angular 变得更强大。

自上一个版本以来，我们完成了两项主要的征求意见请求搞 (RFC)，这为整个 Angular 社区提供了针对提议的更改提供设计反馈的机会。因此，我们的[严格类型反应表单 RFC](https://github.com/angular/angular/discussions/44513)解决了我们的[#1 GitHub 问题](https://github.com/angular/angular/issues/13721)，并且我们的[独立 API RFC](https://github.com/angular/angular/discussions/45554)引入了一种更简单的方法来编写 Angular 应用程序。

我们还将 Angular 组织中存储库中的默认分支重命名为 main，以履行我们对包容性社区的承诺。

此外，此版本还包括由社区成员直接贡献的许多功能和错误修复，从添加路由器强类型到更多 tree-shakable 错误消息。我们很高兴强调 RFC 和社区如何继续使 Angular 成为更多开发人员的选择和拥有更好的开发人员体验！

<!-- more -->

## 1. 使用独立组件简化 Angular

[Angular 独立组件](https://angular.io/guide/standalone-components)旨在通过减少对 NgModule 的需求来简化 Angular 应用程序的编写。在 v14 中，独立组件处于开发者预览版中。它们已准备好在您的应用程序中用于探索和开发，但不是稳定的 API，并且可能会在我们典型的向后兼容性模型之外发生变化。

```ts
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common'; // includes NgIf and TitleCasePipe
import { bootstrapApplication } from '@angular/platform-browser';

import { MatCardModule } from '@angular/material/card';
import { ImageComponent } from './app/image.component';
import { HighlightDirective } from './app/highlight.directive';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [
    ImageComponent,
    HighlightDirective, // import standalone Components, Directives and Pipes
    CommonModule,
    MatCardModule // and NgModules
  ],
  template: `
    <mat-card *ngIf="url">
      <app-image-component [url]="url"></app-image-component>
      <h2 app-highlight>{{ name | titlecase }}</h2>
    </mat-card>
  `
})
export class ExampleStandaloneComponent {
  name = 'emma';
  url = 'www.emma.org/image';
}

// Bootstrap a new Angular application using our `ExampleStandaloneComponent` as a root component.
bootstrapApplication(ExampleStandaloneComponent);
```

对于带有`standalone: true`的独立组件、指令或管道，可以直接在@Component()组件声明中 import，而无需通过@NgModule()来 import.

而且在@Component()中也可以引入一个模块, 这样 Angular 独立组件不仅可以引用 standalone 形式的独立组件、指令或管道也可以兼容以 module 形式组织的旧的物件.

探索新的 [Stackblitz 演示应用程序](https://stackblitz.com/edit/angular-standalone?file=src%2Fmain.ts)，了解有关如何使用独立组件构建 Angular 应用程序的更多信息。

在开发者预览版中，我们希望利用 open source 来为发布稳定的 standalone API 做好充分准备。使用`ng generate component <name> --standalone`来为您的应用添加第一个独立组件，然后前往我们的 [GitHub 存储库](https://github.com/angular/angular/issues/new/choose)提供反馈吧。

在接下来的几个月中，我们将继续构建 schematics（例如`ng new <app-name> --standalone`），并编写更多关于如何使用这样新特性的案例和如何掌握它的学习旅程。请记住，由于目前处于开发者预览中，随着我们不断继续完善我们的设计，有些使用方式可能会在正式稳定版中发生变化。

您可以在[两个 RFC 和公开设计审查](https://github.com/angular/angular/discussions)中阅读有关当前实现背后的设计思想的更多信息。请务必在此处和[Twitter](https://twitter.com/angular)上关注我们，以获取独立 API 的未来更新。

## 2. 类型化的 Angular 表单

Angular v14 解决了 Angular 的 GitHub [顶级问题](https://github.com/angular/angular/issues/13721)：为 Angular Reactive Forms 包实现严格类型。

类型化表单确保表单控件、组和数组内的值在整个 API 表面上都是类型安全的。这使得表单更安全，特别是对于深度嵌套的复杂情况。

```ts
const cat = new FormGroup({
  name: new FormGroup({
    first: new FormControl('Barb'),
    last: new FormControl('Smith')
  }),
  lives: new FormControl(9)
});

// Type-checking for forms values!
// TS Error: Property 'substring' does not exist on type 'number'.
let remainingLives = cat.value.lives.substring(1); //这里直接报编译错误

// Optional and required controls are enforced!
// TS Error: No overload matches this call.
cat.removeControl('lives');

// FormGroups are aware of their child controls.
// name.middle is never on cat
let catMiddleName = cat.get('name.middle');
```

[此功能是公开征求意见和设计审查](https://github.com/angular/angular/discussions/44513)的结果，它建立在 Angular 社区贡献者（包括 Sonu Kapoor、Netanel Basel 和 Cédric Exbrayat ）之前的原型设计、工作和测试的基础上。

用于迁移到这一新功能的 update schematics 允许增量迁移 input 表单，因此您可以逐步向现有表单添加 input 内容，并具有完全向后兼容性。ng update 将用无类型版本替换所有表单类（例如 FormGroup-> UntypedFormGroup）。然后，您可以按照自己的节奏启用类型（例如 UntypedFormGroup-> FormGroup）。

```bash

// v13 untyped form
const cat = new FormGroup({
   name: new FormGroup(
      first: new FormControl('Barb'),
      last: new FormControl('Smith'),
   ),
   lives: new FormControl(9)
});

// v14 untyped form after running `ng update`
const cat = new UntypedFormGroup({
   name: new UntypedFormGroup(
      first: new UntypedFormControl('Barb'),
      last: new UntypedFormControl('Smith'),
   ),
   lives: new UntypedFormControl(9)
});

```

为了利用新的类型支持，我们建议搜索 Untyped 表单控件的实例并尽可能迁移到新类型的表单 API 界面。

```ts

// v14 partial typed form, migrating `UntypedFormGroup` -> `FormGroup`
const cat = new FormGroup({
   name: new FormGroup(
      first: new UntypedFormControl('Barb'),
      last: new UntypedFormControl('Smith'),
   ),
   lives: new UntypedFormControl(9)
});

```

我们建议新应用程序使用 Form\*类，除非该类有意为非类型化（例如，FormArray 同时具有数字和字符串的 a）。在[文档](https://angular.io/guide/typed-forms)中了解更多信息。

## 3. 简化的最佳实践

Angular v14 带来了内置功能，使开发人员能够构建高质量的应用程序，从路由到代码编辑器，从 angular.io 上的新的[更改检测指南](http://angular.io/guide/change-detection)开始。

## 4. 简化的页面标题可访问性

另一个最佳实践是确保应用程序的页面标题能够唯一地传达页面的内容。v13.2 通过 Angular Router 中的新 Route.title 属性简化了这一过程。但是在 v14 版本中添加 title 不必这么麻烦了，并且是强类型的，这要归功于 Marko Stanimirović 的惊人社区贡献。

```ts

const routes: Routes = [{
  path: 'home',
  component: HomeComponent
  title: 'My App - Home'  // <-- Page title
}, {
  path: 'about',
  component: AboutComponent,
  title: 'My App - About Me'  // <-- Page title
}];

```

您通过提供自定义 TitleStrategy, 来配置更加复杂的标题设置逻辑.

```ts

const routes: Routes = [{
  path: 'home',
  component: HomeComponent
}, {
  path: 'about',
  component: AboutComponent,
  title: 'About Me'  // <-- Page title
}];

@Injectable()
export class TemplatePageTitleStrategy extends TitleStrategy {
  override updateTitle(routerState: RouterStateSnapshot) {
    const title = this.buildTitle(routerState);
    if (title !== undefined) {
      document.title = `My App - ${title}`;
    } else {
      document.title = `My App - Home`;
  };
};

@NgModule({
  …
  providers: [{provide: TitleStrategy,  useClass: TemplatePageTitleStrategy}]
})
class MainModule {}

```

在这些示例中，导航到“/about”会将文档标题设置为“我的应用程序 - 关于我”，而导航到“/home”会将文档标题设置为“我的应用程序 - 主页”。

您可以在 Google I/O 2022 研讨会上了解有关使用 Angular 进行无障碍构建的更多信息。

## 5. 加强的开发人员诊断工具

新的[扩展诊断](https://angular.io/extended-diagnostics)提供了一个可扩展的框架，使您可以更深入地了解模板以及如何改进它们。诊断为您的模板提供编译时警告和精确、可操作的建议，在运行时之前捕获错误。

[我们对它为开发人员在未来添加诊断功能](https://blog.angular.io/angular-extended-diagnostics-53e2fa19ece9)引入的灵活框架感到兴奋。

在 v13.2 中，我们包含内置的扩展诊断功能，以帮助开发人员捕获两个最常见的模板错误。

### 5.1. 捕获双向数据绑定上的无效“Banana in a box”错误

开发人员常见的语法错误是在双向绑定中翻转方括号和圆括号，([])将[()]. 由于()排序看起来像香蕉，[]排序看起来像盒子，因此我们将其称为“盒子里的香蕉”错误，因为香蕉应该放在盒子里。

虽然此错误在技术上是有效的语法，但我们的 CLI 可以认识到这很少不是开发人员想要的。在 v13.2 版本中，我们引入了有关此错误的详细消息传递以及有关如何解决此问题的指南，所有这些都在 CLI 和代码编辑器中进行。

```ts

Warning: src/app/app.component.ts:7:25 - warning NG8101: In the two-way binding syntax the parentheses should be inside the brackets, ex. '[(fruit)]="favoriteFruit"'.
        Find more at https://angular.io/guide/two-way-binding
7     <app-favorite-fruit ([fruit])="favoriteFruit"></app-favorite-fruit>
                          ~~~~~~~~~~~~~~~~~~~~~~~~~

```

### 5.2. 捕获不可空值的空值合并

扩展诊断还会引发 Angular 模板中无用的无效合并运算符(??)的错误。具体来说，当输入不是“可为空”时，即表示其类型不包括 null 或 undefined 时，会引发此错误。

ng build 扩展诊断在、ng serve 和 期间使用 Angular 语言服务实时显示为警告。诊断可在 tsconfig.json 中配置，您可以在其中指定诊断是否应为 warning、error 或 suppress。

```json

{
  "angularCompilerOptions": {
    "extendedDiagnostics": {
      // The categories to use for specific diagnostics.
      "checks": {
        // Maps check name to its category.
        "invalidBananaInBox": "error",
        "nullishCoalescingNotNullable": "warning"
      },
      // The category to use for any diagnostics not listed in `checks` above.
      "defaultCategory": "suppress"
    },
    ...
  },
  ...
}

```

在[我们的文档](https://angular.io/extended-diagnostics)和扩展诊断博客文章中了解有关扩展诊断的更多信息。

### 5.3. Tree-shakeable 错误消息

当我们继续编写[Angular 调试指南](https://blog.angular.io/angular-debugging-guides-dfe0ef915036)时， Ramesh Thiruchelvam 为社区贡献了添加新的运行时错误代码。强大的错误代码使您可以更轻松地参考和查找有关如何调试错误的信息。

这允许构建优化器从生产包中 tree-shake 错误消息（长字符串），同时保留错误代码。

```ts

@Component({...})
class MyComponent {}

@Directive({...})
class MyDirective extends MyComponent {}  // throws an error at runtime

// Before v14 the error is a string:
> Directives cannot inherit Components. Directive MyDirective is attempting to extend component MyComponent.

// Since v14 the error code makes this tree-shakeable:
> NG0903: Directives cannot inherit Components. Directive MyDirective is attempting to extend component MyComponent.

// v14 production bundles preserve the error code, tree-shaking strings and making the bundle XX smaller:
> NG0903

```

要调试生产错误，我们建议前往[参考指南](https://angular.io/errors/)并在开发环境中重现错误，以便查看完整的字符串。我们将继续逐步重构现有错误，以便在未来的版本中利用这种新格式。

## 6. 更多内置改进

v14 包括对最新 TypeScript 4.7 版本的支持，现在默认以 ES2020 为目标，这使得 CLI 可以在不降级的情况下发布更小的代码。

此外，我们还想强调三个特色：

### 6.1. 绑定到 protected 的组件成员变量

在 v14 中，您现在可以直接从模板绑定到受保护的组件成员，这要感谢 Zack Elliott 的贡献！

```ts
@Component({
  selector: 'my-component',
  template: '{{ message }}' // Now compiles!
})
export class MyComponent {
  protected message: string = 'Hello world';
}
```

这使您可以更好地控制可重用组件的公共 API 界面。

### 6.2. 嵌入式视图中的可选注入器

ViewContainerRef.createEmbeddedView v14 中添加了在通过和创建嵌入视图时传入可选注入器的支持 TemplateRef.createEmbeddedView。注入器允许在特定模板内自定义依赖注入行为。

这使得能够使用更清晰的 API 来编写可重用组件以及 Angular CDK 中的组件原生类型。

```ts
viewContainer.createEmbeddedView(templateRef, context, {
  injector: injector
});
```

### 6.3. NgModel OnPush 模型

最后，Artur Androsovych 的社区贡献解决了一个热门问题，并确保 NgModel 更改反映在 OnPush 组件的 UI 中。

```ts
@Component({
  selector: 'my-component',
  template: ` <child [ngModel]="value"></child> `,
  changeDetection: ChangeDetectionStrategy.OnPush
})
class MyComponent {}
```

## 7. 内置原始类型和工具

CDK 和工具改进为更强大的开发环境提供了构建块，从 CDK 菜单原语到 CLI 自动完成。

Angular 的组件开发工具包提供了一整套用于构建 Angular 组件的工具。在 v14 中，我们将 CDK 菜单和对话框提升至稳定！

此版本包括新的 CDK 原始类型，可用于基于 WAI-ARIA 菜单和菜单栏设计模式创建更易于访问的自定义组件。

```html
<ul cdkTargetMenuAim cdkMenuBar>
  <li cdkMenuItem [cdkMenuTriggerFor]="file">File</li>
</ul>
<ng-template #file>
  <ul cdkMenu cdkTargetMenuAim>
    <li cdkMenuItem>Open</li>
  </ul>
</ng-template>
```

## 8. hasHarness 和组件测试工具中的 getHarnessOrNull

v14 为 HarnessLoader 添加了新方法用于检查 harness 是否存在并返回 harness 实例（如果存在）。组件测试工具继续提供一种灵活的方式来为组件编写更好的测试。

## 9. Angular CLI 增强功能

标准化 CLI 参数解析意味着整个 Angular CLI 具有更高的一致性，现在每个标志都使用--lower-skewer-case 格式。我们删除了已弃用的驼峰式大小写参数支持，并添加了对组合别名使用的支持。

好奇这意味着什么？运行 ng --help 以获得更清晰的输出来解释您的选项

### 9.1. NG 命令自动补全

意外输入 ng sevre 而不是 ng serve 经常发生。拼写错误是命令行提示符引发错误的最常见原因之一。为了解决这个问题，v14 的新功能 ng completion 引入了实时提前输入自动完成功能！

为了确保所有 Angular 开发人员都了解这一点，CLI 将提示您在 v14 中的第一次命令执行期间选择自动完成。您也可以手动运行 ng completion，CLI 会自动为您进行设置。

### 9.2. NG analytics

CLI 的 analytics 命令允许您控制分析设置和打印分析信息。更详细的输出可以清楚地传达您的分析配置，并为我们的团队提供遥测数据，以告知我们的项目优先级。当您打开它时，它会非常有帮助！

### 9.3. ng 缓存

ng cache 提供了一种从命令行控制和打印缓存信息的方法。您可以启用、禁用或从磁盘删除，以及打印统计信息和信息。

## 10. Angular DevTools 可离线使用并可在 Firefox 中使用

感谢 Keith Li 的社区贡献，Angular DevTools 调试扩展现在支持离线使用。对于 Firefox 用户，请在 Mozilla 附加组件中找到该扩展。

## 11. 实验性 ESM 应用程序构建

最后，v14 引入了一个基于 esbuild 的实验性构建系统 ng build，该系统编译纯 ESM 输出。

要在您的应用程序中尝试此操作，请更新您的浏览器构建器 angular.json：

```json

"builder": "@angular-devkit/build-angular:browser"

"builder": "@angular-devkit/build-angular:browser-esbuild"

```

随着我们不断增加对 Sass 等样式表预处理器的支持，我们的团队很高兴收集有关您应用程序性能的反馈。

## 12. 下一步是什么

随着此版本的发布，我们还更新了公开路线图，以反映当前和未来团队项目和探索的状态。

您可以在 Angular 博客和#GoogleIO 的 State of Angular 中了解有关我们团队未来计划的更多信息：

感谢所有每天构建、创新和激励我们的出色开发人员 — 我们对 Angular 的发展方向感到兴奋！

前往 update.angular.io 并通过@Angular 向我们发送推文，了解您的#ngUpdate 体验！

## 13. 相关文章

最新更新以及更多 Angular 相关文章请访问 [Angular 专题 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

<!--
## 参考文档

[Angular v14 is now available!](https://blog.angular.io/angular-v14-is-now-available-391a6db736af)
 -->
