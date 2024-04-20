---
title: Angular async pipe中的异常处理
date: 2024/03/23
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

处理 web 应用程序中的错误对于获得良好的用户体验非常重要。有时会发生异常，每个应用程序都应该涵盖这些情况，以帮助用户了解发生了不好的事情。

许多教程没有显示如何在使用异步管道时处理错误/异常情况。因此，在这篇文章中，我们将研究如何处理这些错误案例的一些技术。

<!-- more -->

一个简单的异步管道示例
让我们看看您可能在许多教程中看到的一个示例：

```ts

@Component({
  selector: "app-mycomponent",
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div *ngIf="users$ | async as users; else loading">
      <div *ngFor="let user of users">
        {{ user.name }}
      </div>
    </div>

    <ng-template #loading> Loading users... </ng-template>
  `,
})
class MyComponent {
  users$: Observable<User[]>;

  constructor(httpClient: HttpClient) {
    this.users$ : httpClient.get<User[]>("/api/users");
  }
}

```

异步管道订阅$users observable。第一种状态是“加载”状态，因为 Observable 还没有发出值，所以\*ngIf 中的 else 情况是 active 的。当 HTTP 请求以 2xx 状态代码响应时，一切都很好：用户将看到用户列表。

这个例子只是一个 happy case, 并没有考虑 observable 出现异常的情况。当 HTTP 请求以错误结束时，用户在页面上看不到任何错误消息。他仍然会看到加载状态，这与用户在这种情况下的预期相去甚远。那么，我们如何更好地处理这一问题，并向用户表明发生了不好的事情？

## 1. 一个简单的解决方案

让我们从上面扩展我们的示例，以便能够处理所有三种状态（加载、成功、错误）：

```ts
import { ChangeDetectionStrategy, Component } from "@angular/core";
import { Observable, Subject, of } from "rxjs";
import { catchError } from "rxjs/operators";
import { HttpClient, HttpClientModule } from "@angular/common/http";
import { CommonModule } from "@angular/common";

@Component({
  standalone: true,
  selector: "app-mycomponent",
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <p>hi</p>
    <div *ngIf="users$ | async as users; else loadingOrError">
      <div *ngFor="let user of users">
        {{ user.name }}
      </div>
    </div>

    <ng-template #loadingOrError>
      <div *ngIf="loadingError$ | async; else loading">
        Error loading the list of users. Please try again later.
      </div>
      <ng-template #loading>Loading users...</ng-template>
    </ng-template>
  `,
  imports: [CommonModule, HttpClientModule],
})
export class MyComponent {
  users$: Observable<User[]>;
  loadingError$: Subject<boolean> = new Subject<boolean>();

  constructor(httpClient: HttpClient) {
    this.users$ = httpClient.get<User[]>("/api/users").pipe(
      catchError((error) => {
        // it's important that we log an error here.
        // Otherwise you won't see an error in the console.
        console.error("error loading the list of users", error);
        this.loadingError$.next(true);
        return of();
      })
    );
  }
}

export interface User {
  name: string;
}
```

当创建组件时，我们会为 users$ observable 创建一个处理错误状态的 ng template #loadingOrError 和一个接受信息的 observableerrorLoading$。

如果发生错误，我们将使用 catchError 运算符捕获错误。catchError 的作用是，您将不再在 devtools 控制台中看到错误，这对调试错误情况没有太大用处。接下来，我们为 loadingError$主题发出一个新值。最后一个重要步骤是用 of 算子恢复可观测值，并给它一个 false 值。您也可以使用 null。

## 2. 分离异常数据

上面显示的解决方案基本能解决问题，但是还有很大的改善空间。所以，让我们看看如何做得更好一点。

记住我们的目标，我们的目标是将加载过程显示和错误处理抽取到一个独立的组件中，提高代码的可重用性，并且尽量减少对现有 async pipe 结构的侵入性。

回顾以上解决方案，我们发现有两个问题，首先第一点，正常数据流与异常数据流，紧密耦合在一起，这不利于我们朝目标方向进行重构。

第二点，我们完全创建了一个全新的 loadingError$ subject,这个有点多余，而且它只接受 boolean 对象，这不利于我们向用户展示详细错误信息， 我们可以在 users$ observable 的基础上分岔出一个加载异常流。

下面我们就以上两点进行改进。

```ts
import { CommonModule } from "@angular/common";
import { HttpClient, HttpClientModule } from "@angular/common/http";
import { ChangeDetectionStrategy, Component } from "@angular/core";
import { Observable, of } from "rxjs";
import { catchError, ignoreElements, shareReplay } from "rxjs/operators";

@Component({
  standalone: true,
  selector: "app-mycomponent",
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div *ngIf="users$ | async as users; else loadingOrError">
      <div *ngFor="let user of users">
        {{ user.name }}
      </div>
    </div>

    <ng-template #loadingOrError>
      <div *ngIf="loadingError$ | async as originalErrorMsg; else loading">
        <p>Error loading the list of users.</p>
        <p>{{ originalErrorMsg }}</p>
        <p>Please try again later.</p>
      </div>
      <ng-template #loading>Loading users...</ng-template>
    </ng-template>
  `,
  imports: [CommonModule, HttpClientModule],
})
export class MyComponent {
  users$: Observable<User[]>;
  loadingError$: Observable<string>;

  constructor(httpClient: HttpClient) {
    this.users$ = httpClient.get<User[]>("/api/users");

    this.loadingError$ = this.users$.pipe(
      shareReplay(1),
      ignoreElements(), // 忽略数据元素，避免数据元素对异常数据流的影响
      catchError((error: any) => {
        // it's important that we log an error here.
        // Otherwise you won't see an error in the console.
        console.error("error loading the list of users", error);
        //this.loadingError$.next(true)
        return of(error.message);
      })
    );
  }
}

export interface User {
  name: string;
}
```

通过努力，我们将正常数据流和异常数据流行进行了分离，正常数据流通过 users$ Observable 获取，如果数据正在加载过程中或者加载过程中出现异常，将会显示 loadingOrError template 所定义的内容。

如果出现异常，loadingOrError template 将会向用户展示异常的详细信息。接下来我们将 loadingOrError 抽取为一个组件，以便在其他页面中重用。

## 3. 重构，抽取成可重用组件

我们将处理加载过程和展示错误信息的部分抽取成组件，暂且命名为 loadingOrError

LoadingOrError.component.ts

```ts
import { CommonModule, NgIfContext } from "@angular/common";
import { Component, Input, TemplateRef, ViewChild } from "@angular/core";
import { Observable, of } from "rxjs";
import { catchError, ignoreElements, shareReplay, tap } from "rxjs/operators";

@Component({
  standalone: true,
  selector: "loading-or-error",
  templateUrl: "./LoadingOrError.component.html",
  imports: [CommonModule],
})
export class LoadingOrErrorComponent {
  loadingError$: Observable<string>;

  /**
   * The loading wrapper that should be used to show the loading/error state
   */
  @Input() set loadingWrapper(data: Observable<any>) {
    this.loadingError$ = data.pipe(
      shareReplay(1),
      ignoreElements(),
      catchError((error: any) => {
        console.log(error.message);
        return of(error.message);
      })
    );
  }

  /**
   * A configurable error message for error cases.
   */
  @Input() userDefinedMessage: string = "";
}
```

> 注意，这里我们添加了 ignoreElements rxjs 算子, 这样正常数据流就被过滤掉了，从而形成一个纯粹的异常数据流。

LoadingOrError.component.html

```html
<div *ngIf="loadingError$ | async as originalErrorMsg; else loading">
  {{ userDefinedMessage + ' | ' + originalErrorMsg }}
</div>
<ng-template #loading>Loading...</ng-template>
```

这样我们就将 loading 和 error handling 抽取出成了一个独立组件。接下来我们在业务处理页面使用它。

## 4. 使用 LoadingOrError 组件

src/app/app.component.html

```html
<div *ngIf="users$ | async as users; else loading">
    <div *ngFor="let user of $any(users)">
        <p>{{ user.name }}</p>
    </div>
</div>

<ng-template #loading>
    <loading-or-error
        #loadingOrError
        [loadingWrapper]="users$"
        [userDefinedMessage]="'Error loading the list of users'"
    ></loading-or-error>
</ng-template>

```

从以上使用方法可以看出，我们依然保持了 async pipe 的写法，对 html template, 中 async pipe 部分没有任何侵入性，对数据流的创建过程也没有任何的侵入性。

src/app/app.component.ts

```ts
import { ChangeDetectionStrategy, Component } from "@angular/core";
import { Observable } from "rxjs";
import { TestDataService, User } from "./services/TestDataService";
import { HttpClient } from "@angular/common/http";

@Component({
  selector: "app-root",
  changeDetection: ChangeDetectionStrategy.OnPush,
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.scss"],
})
export class AppComponent {
  users$: Observable<User[]>;

  constructor(httpClient: HttpClient) {
    this.users$ = httpClient.get<User[]>("/api/users");
  }
}
```

## 5. 总结

我们组件的代码现在简单多了：我们在一个通用的、可重用/可配置的组件中处理了错误和加载情况，在几乎不改变原有代码结构的基础上，添加了错误处理功能，它为我们节省了一些代码，否则我们必须在每个组件中编写这些代码。

我很好奇你在项目中是如何处理这些案例的！如果您使用下面的评论框谈论您的解决方案和上面的解决方案，我将非常高兴。

## 6. 相关文章

最新更新以及更多 Angular 相关文章请访问 [Angular 专题 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 7. 参考文档

[How to Handle Errors Reactively when Using the Async Pipe](https://eliteionic.com/tutorials/handle-errors-reactively-when-using-async-pipe/)

[Error Handling with Angular`s async Pipe](https://sebastian-holstein.de/post/2018-02-26-error-handling-angular/)
