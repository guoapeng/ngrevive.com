---
title: Angular应用如何使用百度统计
date: 2023/11/10
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

Angulartics2 是一个用于 Angular 应用程序的分析工具。它可以自动跟踪导航事件并将其发送到您的分析服务提供程序，例如百度统计, Google Analytics 等。您可以通过运行`npm i angulartics2`在项目中开始使用 angulartics。

<!-- more -->

## 1. 获取百度统计账号

关于如何获取百度统计账号, 可以访问我以前的博客[百度统计的使用](https://www.pengtech.net/product/baidu_analytics.html)

## 2. 安装与配置 angulartics2

在命令行中运行`npm i angulartics2`以安装 angulartics2.

### 2.1. 配置 angulartics2

1. 将 angulartics2 添加到项目的根模块(root module)

```ts

import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule, Routes } from '@angular/router';

import { Angulartics2Module } from 'angulartics2';
import { Angulartics2GoogleAnalytics } from 'angulartics2';

const ROUTES: Routes = [
  { path: '',      component: HomeComponent },
  { path: 'about', component: AboutComponent },
];

@NgModule({
  imports: [
    BrowserModule,
    RouterModule.forRoot(ROUTES),

    // added to imports
    Angulartics2Module.forRoot(), // 添加Angulartics2Module 模块
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent],
})

```

> 注意: 如果是使用[Standalone component](https://angular.io/guide/standalone-components)引导启动的 Angular 应用, 需要在
> ApplicationConfig 配置中引入 Angulartics2Module
>
> ```ts
> importProvidersFrom(Angulartics2Module.forRoot()),
> ```

2. 在项目的根组件(root component)中引入相应的分析服务 provider, 例如 Angulartics2BaiduAnalytics
   并调用tracking

   ```typescript
    // in root component
    import { Angulartics2BaiduAnalytics } from 'angulartics2';

    @Component({  ...  })
    export class AppComponent {
      constructor(angulartics2BaiduAnalytics: Angulartics2BaiduAnalytics) {
        angulartics2BaiduAnalytics.startTracking();
      }
    }
   ```

## 3. 使用 angulartics2

前面两部导入Angulartics2Module 和 startTracking 是必要的两步. 完成以上操作后, 我们就可以开始使用angulartics2BaiduAnalytics.

### 3.1. 跟踪模板/HTML中的事件

要跟踪事件，您可以将指令angulartics2On注入任何组件，并使用属性angulartics3On、angularticsAction和angularticsCategory：

```ts
// component
import { Component } from '@angular/core';

@Component({
  selector: 'song-download-box',
  template: `
    <div 
      angulartics2On="click" 
      angularticsAction="DownloadClick" 
      [angularticsCategory]="song.name">
      Click Me
    </div>
  `,
})
export class SongDownloadBox {}

import { NgModule } from '@angular/core';
import { Angulartics2Module } from 'angulartics2';

@NgModule({
  imports: [
    Angulartics2Module,
  ],
  declarations: [
    SongDownloadBox,
  ]
})

```

如果需要事件标签，可以使用

```ts

<div 
  angulartics2On="click" 
  angularticsAction="DownloadClick" 
  angularticsLabel="label-name" 
  angularticsValue="value" 
  [angularticsCategory]="song.name" 
  [angularticsProperties]="{'custom-property': 'Fall Campaign'}">
  Click Me
</div>

```

### 3.2. 使用代码跟踪事件

```ts
import { Angulartics2 } from 'angulartics2';

constructor(private angulartics2: Angulartics2) {
  this.angulartics2.eventTrack.next({ 
    action: 'myAction', 
    properties: { category: 'myCategory' },
  });
}

```

如果需要事件标签，可以使用

```ts

this.angulartics2.eventTrack.next({ 
  action: 'myAction',
  properties: { 
    category: 'myCategory', 
    label: 'myLabel',
  },
});

```

## 4. Angulartics2 高级配置

### 4.1. 排除跟踪一些页面

传递字符串文字或正则表达式以从自动页面跟踪中排除一些页面。

```ts

Angulartics2Module.forRoot({
  pageTracking: {
    excludedRoutes: [
      /\/[0-9]{4}\/[0-9]{2}\/[a-zA-Z0-9|\-]*/,
      '2017/03/article-title'
    ],
  }
}),

```

### 4.2. 从url路径中删除ID

有时如果统计完整的url路径, 在统计报告中看起来非常琐碎, 所以需要去除一些信息, 例如id, 我们可以像下面这样去配置.

```ts
Angulartics2Module.forRoot({
  pageTracking: {
    clearIds: true,
  }
}),
```

默认情况下，它会删除与此模式匹配的ID（即，所有数字或UUID）：`^\d+$|^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$`.

如果需要，您可以设置自己的regexp：

例如使得/project/a01/feature变成/project/feature

```ts
Angulartics2Module.forRoot({
  pageTracking: {
    clearIds: true,
    idsRegExp: new RegExp('^[a-z]\\d+$') /* Workaround: No NgModule metadata found for 'AppModule' */
  }
}),

```

### 4.3. 从url路径中删除查询参数

这可以与clearId和idsRegExp组合使用

/project/12981/feature?param=12变为/project/12981/feature

```ts

Angulartics2Module.forRoot({
  pageTracking: {
    clearQueryParams: true,
  }
}),

```

### 4.4. 从url路径中删除hash

/callback#authcode=123&idToken=456 变成 /callback

```ts
Angulartics2Module.forRoot({
  pageTracking: {
    clearHash: true,
  }
}),
```

### 4.5. 在没有路由器的情况下使用

警告：此支持仍然是实验性的

@仍必须安装angular/router！但是，它不会被使用。

```ts

import { Angulartics2RouterlessModule } from 'angulartics2';
@NgModule({
  // ...
  imports: [
    BrowserModule,
    Angulartics2RouterlessModule.forRoot(),
  ],
})

```

### 4.6. 与UI路由器一起使用

警告：此支持仍然是实验性的

@仍必须安装angular/router！但是，它不会被使用。

```ts
import { Angulartics2UirouterModule } from 'angulartics2';
@NgModule({
  // ...
  imports: [
    BrowserModule,
    Angulartics2UirouterModule.forRoot(),
  ],
})
```

## 5. trouble shooting

1. 问题一, 当运行server sider rendering 时, 报错ReferenceError: _hmt is not defined

```bash

 Build at: 2023-11-16T08:00:28.460Z - Hash: b945708904bd624f - Time: 78602ms
⠙ Prerendering 13 route(s) to D:\dev\proj\demo-angular-project\dist\demo-angular-web\browser...ReferenceError: _hmt is not defined
    at new Angulartics2BaiduAnalytics (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:199481:12)
    at Object.Angulartics2BaiduAnalytics_Factory [as factory] (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:199536:10)
    at D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:134269:33
    at runInInjectorProfilerContext (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:125777:5)
    at R3Injector.hydrate (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:134268:9)
    at R3Injector.get (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:134148:23)
    at R3Injector.get (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:134157:27)
    at ChainedInjector.get (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:138783:32)
    at lookupTokenUsingModuleInjector (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:129505:31)
    at getOrCreateInjectable (D:\dev\proj\demo-angular-project\dist\demo-angular-web\server\main.js:129551:10)
✖ Prerendering routes to D:\dev\proj\demo-angular-project\dist\demo-angular-web\browser failed.
_hmt is not defined

```

问题原因: 因为时server side 而不是浏览器中运行angulartics2, 所以找不到_hmt

解决办法:

1. 我已经提交[issue](https://github.com/angulartics/angulartics2/issues/476)给angulartics2, 正在等待它们回复.

2. 这里有一个[类似的问题](https://stackoverflow.com/questions/60667522/is-it-possible-to-avoid-page-tracking-root-route-to-ga-by-angulartics2), 里面提到了一种workaround, 即通过服务去访问angulartics2, 定义一个专门针对server side的service, 在服务中避免调用angulartics2

## 6. 相关文章

最新更新以及更多 Angular 相关文章请访问 [Angular 专题 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 7. 参考文档

[angulartics2](https://github.com/angulartics/angulartics2)
