---
title: 深入了解 Angular 异步管道
date: 2024/03/17
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

Angular 异步管道(Async pipe)是 Angular 框架中在 HTML 模板中使用 Observables 或 Promises 的方法之一。而且在大多数情况下，我认为这是在 Angular 中处理 Observables 和 Promise 的首选方式。

当您使用异步管道订阅 Observable 或 Promise 时，您将收到 Observable 或 Promise 最后发出的值。

请注意，在 Observable 或 Promise 发出值之前，需要在 HTML 模板中初始化异步管道，否则管道不会接收任何值。

因此，如果异步管道位于 \*ngIf 内，并且 Observable 在 ngIf 计算为 true 之前发出一个值，则不会显示任何内容，并且异步管道在发出新值之前不会接收任何值。

使用 Angular 异步管道很简单，并且比订阅 Observables 和 Promise 的一些更标准的方法提供了一些好处。

<!-- more -->

## 1. 为什么使用 Angular 异步管道？

在 Angular 中，当您订阅 Observable 或 Promis 时，您需要在组件被销毁（或 Observable 不再使用）时取消订阅。如果您忘记取消订阅，则会出现内存泄漏，从而给您的应用程序带来各种麻烦。使用 Angular 异步管道，当组件被销毁时，订阅会自动取消订阅。此外，如果您使用新的 observable 重新分配与异步管道一起使用的属性，它会自动取消订阅旧的并订阅新分配的 Observable 或 Promise。这大大降低了 Angular 应用程序中内存泄漏的风险。

另一个好处是，异步管道在收到新值时会将模板标记为要检查更改。当您将更改检测策略设置为 OnPush 时，这会简化很多手工检查操作。

对于常规订阅(非异步管道订阅)，如果您使用 OnPush 策略，则 HTML 模板不会用于检查更改，这意味着您必须在订阅收到新值后自行触发更改检测。因此，通过将异步管道与 OnPush 策略结合使用，您可以获得两方面的好处，自动更新 HTML 模板，而无需自己触发它，也无需 Angular 定期检查它。结合使用异步管道和 OnPush 更改检测策略是提高 Angular 应用程序性能的好方法。

## 2. 如何使用 Angular 异步管道？

要开始使用 Angular 异步管道，您需要在 TypeScript 文件中创建一个 Observable 属性，并为其分配一个 Observable 对象。现在，您可以在 HTML 模板中使用此属性，并在模板中使用 async 管道订阅 Observable 对象。异步管道将为您订阅可观察对象并解析 HTML 模板内的值。当组件被销毁时，异步管道将自动取消订阅可观察对象。在以下示例中，我创建了一个示例服务，其中包含我们订阅的 RxJs 主题，以及一个简单的方法来通过超时来更新其值，该超时会模拟我们真实场景中 Observable 的处理时间。

```ts
import { Injectable } from "@angular/core";
import { Observable, Subject } from "rxjs";

@Injectable({
  providedIn: "root",
})
export class TestServiceService {
  private readonly _testSubject: Subject<any> = new Subject<any>();
  public testSubject: Observable<any> = this._testSubject.asObservable();

  constructor() {}

  updateTestBehaviorSubject(value: any, timeout: number = 500) {
    setTimeout(() => {
      this._testSubject.next(value);
    }, timeout);
  }
}
```

在 TypeScript 文件中，您可以看到我们分配可观察值的属性。属性名称以 $ 为前缀，这是可观察量的常见做法。Typescript 文件中还有一个函数调用来更新服务中的 Observable 值。

```ts
import { Component } from "@angular/core";
import { TestServiceService } from "./services/TestServiceService";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.scss"],
})
export class AppComponent {
  $testObservable = this.testService.testSubject;

  constructor(private testService: TestServiceService) {}

  ngOnInit(): void {
    this.testService.updateTestBehaviorSubject("Some observable test value");
  }
}
```

现在我们可以在 HTML 模板中使用$testObservable 属性以及异步管道来接收其值并将其显示在我们的视图中，如下所示：

```html
<div>{{$testObservable | async}}</div>
```

通过使用异步管道，我们可以编写更少的代码，并且我们编写的代码不太容易出现内存泄漏。如果我们在没有异步管道的情况下执行相同的操作，我们的代码将如下所示：

```ts
import { TestServiceService } from "./test-service.service";
import { Component } from "@angular/core";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.scss"],
})
export class AppComponent {
  $testSubscription: any;
  testObservableValue: any;

  constructor(private testService: TestServiceService) {}

  ngOnInit(): void {
    this.testService.updateTestBehaviorSubject("Some observable test value");
    this.$testSubscription = this.testService.testSubject.subscribe(
      (value) => (this.testObservableValue = value)
    );
  }

  ngOnDestroy(): void {
    //Called once, before the instance is destroyed.
    //Add 'implements OnDestroy' to the class.
    this.$testSubscription.unsubscribe();
  }
}
```

正如您所看到的，当我们不使用异步管道时，TypeScript 文件中需要更多代码。当订阅数量增加时，您忘记取消订阅的机会也会增加，从而导致很多麻烦。当您处理多个订阅时，样板代码也会开始堆积，使您的文件变得更加臃肿，并且更难以阅读和维护。

但现在您可能会问自己，当我想在订阅的括号内执行某些逻辑时会发生什么，如何使用异步管道处理？对于这个 Rxjs 使用 Pipe 和 Map 来救援！在将值发送到 HTML 模板之前，我们可以对可观察对象进行管道和映射以添加我们想要的任何逻辑。作为示例，我将在将可观察值发送回 HTML 之前向其添加一些文本：

```ts
import { Component } from "@angular/core";
import { TestServiceService } from "./services/TestServiceService";
import { map } from "rxjs";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.scss"],
})
export class AppComponent {
  $testObservable = this.testService.testSubject.pipe(
    map((val) => {
      return val + " + Added value";
    })
  );

  constructor(private testService: TestServiceService) {}

  ngOnInit(): void {
    this.testService.updateTestBehaviorSubject("Some observable test value");
  }
}
```

## 3. 以多种方式使用异步管道

您可以在 HTML 模板中以多种方式使用异步管道及其生成的值。例如，您可能想要检查 *ngIf 或 *ngFor 内的值，或者在 HTML 模板中的多个位置使用该值。让我们看一下多种场景以及处理此问题的方法。

### 3.1. 在模板变量中使用异步管道

在某些情况下，您可能需要 HTML 模板中多个位置的异步管道的解析值。实现此目的的一种方法是在模板中多次使用异步管道，但这会创建多个订阅，从而影响性能。

```ts
import { TestServiceService } from "./test-service.service";
import { ChangeDetectionStrategy, Component } from "@angular/core";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.scss"],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class AppComponent {
  $testObservable = this.testService.testSubject;

  constructor(private testService: TestServiceService) {}

  ngOnInit(): void {
    this.testService.updateTestBehaviorSubject({
      title: "Some title",
      description: "Test description",
    });
  }
}
```

```html
<div>
  <p>{{( $testObservable | async)?.title }}</p>
  <p>{{( $testObservable | async)?.description }}</p>
</div>
```

这确实有效，但我们肯定可以做得更好，现在让我们为异步管道分配一个变量名称，这样我们就可以在多个地方使用该值，而无需多次使用异步管道。最常见的方法之一是在父元素上使用 *ngIf，并在 *ngIf 内使用异步管道，

这样您就可以安全地使用子元素中的值。只需用分号结束异步管道，并在其后创建一个 let 变量即可将异步值分配给该变量。

```html
<div *ngIf="$testObservable | async; let testValue">
  <p>{{testValue.title }}</p>
  <p>{{testValue.description }}</p>
</div>
```

如果并非所有子元素都需要等待异步管道来解析值，并且没有明确的 HTML 标记来放置异步管道，您可以使用 ng-container 并将带有变量名称的异步管道放置在其上。

```html
<div>
  <p>Some other value that does not need to wait on the observable</p>

  <ng-container *ngIf="$testObservable | async; let testValue">
    <p>{{testValue.title }}</p>
    <p>{{testValue.description }}</p>
  </ng-container>
</div>
```

现在让我们修改代码通过将数组传递给 Observable：

```ts
this.testService.updateTestBehaviorSubject([
  { title: "Some title", description: "Test description" },
  { title: "Some title", description: "Test description" },
]);
```

您还可以在 HTML 模板中将异步管道与 \*ngFor 循环结合使用，如下所示：

```html
<div *ngFor="let item of $testObservable | async;">
  <p>{{item.title }}</p>
  <p>{{item.description }}</p>
</div>
```

### 3.2. 处理多个异步管道

最后一件事是如何在一个 HTML 模板中处理多个异步管道。假设我们的 HTML 模板中有 3 个不同的可观察量，并且所有这些都在 ngIf 中使用，如下所示：

```html
<div *ngIf="$testObservable | async; let testValue">
  <p>{{testValue[0].title }}</p>
  <p>{{testValue[0].description }}</p>
</div>
<div *ngIf="$testObservable | async; let testValue2">
  <p>{{testValue2[0].title }}</p>
  <p>{{testValue2[0].description }}</p>
</div>

<div *ngIf="$testObservable3 | async; let testValu3">
  <p>{{testValu3[0].title }}</p>
  <p>{{testValu3[0].description }}</p>
</div>
```

当可观察量的数量增加时，HTML 模板的复杂性也会增加。这可能会导致 UI 错误，其中某些元素先于其他元素显示，或者无法获取其异步值，因为它位于另一个解析太晚的元素内部，等等。有一种更简洁的方法来处理这个问题，即创建一个 ng-container 元素，在其中一次性解析所有异步管道并将其分配给一个变量。

```html
<ng-container
  *ngIf="{
  obser1: $testObservable | async,
  obser2: $testObservable2 | async,
  obser3: $testObservable3 | async
} as data"
>
  <div>
    <p>{{data.obser1[0].title }}</p>
    <p>{{data.obser1[0].description }}</p>
  </div>
  <div>
    <p>{{data.obser2[0].title }}</p>
    <p>{{data.obser2[0].description }}</p>
  </div>

  <div>
    <p>{{data.obser2[0].title }}</p>
    <p>{{data.obser2[0].description }}</p>
  </div>
</ng-container>
```

## 4. 异步加载动画

当我们从服务器端获取数据时，通常有一个漫长的等等过程，数据在加载过程中，会出现白屏的情况，此时用户会不知所措，从而导致用户体验较差。
此时我们可以在数据加载时显示加载动画，让用户直到数据正在加载，从而耐心等待数据加载完成。

```html
<div *ngFor="let item of $testObservable | async; else loading">
  <p>{{item.title }}</p>
  <p>{{item.description }}</p>
</div>

<ng-template #loading>
  <ion-card>
    <ion-card-content>
      <ion-skeleton-text animated></ion-skeleton-text>
    </ion-card-content>
  </ion-card>
</ng-template>
```

## 5. 异步管道异常处理

以下是一个异常处理的示例，其能够处理所有三种状态（加载、成功、错误）：

```ts

import { Observable } from "rxjs/Observable";
import { of } from "rxjs/observable/of";
import { catchError } from "rxjs/operators";
import { Subject } from "rxjs/Subject";

@Component({
  selector: "app-mycomponent",
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div *ngIf="users$ | async as users; else loadingOrError">
      <div *ngFor="let user of users">
        {{ user.name }}
      </div>
    </div>

    <ng-template #loadingOrError>
      <div *ngIf="errorLoading$ | async; else loading">
        Error loading the list of users. Please try again later.
      </div>
      <ng-template #loading> Loading users... </ng-template>
    </ng-template>
  `,
})

export class LoadingWrapper<T> {
  private readonly _errorLoading$ : new Subject<boolean>();
  readonly errorLoading$: Observable<boolean> : this._errorLoading$.pipe(
    shareReplay(1)
  );
  readonly data$: Observable<T>;

  constructor(data: Observable<T>) {
    this.data$ : data.pipe(
      shareReplay(1),
      catchError((error) => {
        console.log(error);
        this._errorLoading$.next(true);
        return of();
      })
    );
  }
}

```

当组件被创建时，我们有一个 users$可观察的状态，因此 loadOrError ng-template 被创建。接下来，我们还有一个errorLoading$可观察的“假”状态，因此“正在加载用户...”文本是可见的。

如果发生错误，我们将使用 catchError 运算符捕获错误。其 effect 作用 catchError 是您将不再在 devtools 控制台中看到错误，这对于调试错误情况并不是非常有用。所以我们添加一个 console.error 并记录给定的错误。接下来，我们为该 loadingError$主题发出一个新值。最后一个重要的步骤是使用运算符恢复可观察值 of 并赋予它一个假值。您也可以使用 null.

更多 async pipe 异常相关内容，请参考[Angular async pipe 中的异常处理](https://www.pengtech.net/angular/angular_async_pipe_error_handling.html)

## 6. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 7. 结论

使用 Angular 异步管道是减少样板代码、在处理可观察量的方式上创建一定一致性的好方法，并且可以最大限度地减少数据泄漏的变化。我建议尽可能使用异步管道，它将提高应用程序的性能和可维护性。将异步管道与 OnPush 更改检测结合使用甚至可以通过在收到新值时自动标记要检查更改的 HTML 模板来使事情变得更好。

## 8. 参考文档

[Angular async pipe in depth](https://writtenforcoders.com/blog/angular-async-pipe-in-depth)

[How to Handle Errors Reactively when Using the Async Pipe](https://eliteionic.com/tutorials/handle-errors-reactively-when-using-async-pipe/)

[Error Handling with Angular`s async Pipe](https://sebastian-holstein.de/post/2018-02-26-error-handling-angular/)
