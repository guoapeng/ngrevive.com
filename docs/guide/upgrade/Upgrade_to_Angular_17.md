---
title: 一步一步升级到Angular 17
date: 2024/03/30
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

Angular 16 于 2023 年 11 月 8 日发布.

今天，我计划向您介绍如何升级到 Angular 17。

<!-- more -->

## 1. 升级前准备

升级前到 Angular 17 之前, 请确保当前 Angular 已经升级到 Angular 16, 并完成了 Angular 16 所有的迁移任务. 如果没有完成 Angular 13 到 14, 14 到 15, 以及 15 到 16 的升级任务, 可以参考我的博客[从 Angular 13 升级到 Angular 15 | 鹏叔的技术博客](https://www.pengtech.net/angular/angular_upgrade_13_to_15.html)，[一步一步升级到 Angular 16](https://www.pengtech.net/angular/Upgrade_to_Angular_16.html)

升级前需要了解 Angular 17 与 Typescript, nodejs 等的版本兼容信息. 可以参考[Actively supported versions](https://angular.io/guide/versions)

Actively supported versions

| ANGULAR            | NODE.JS                              | TYPESCRIPT     | RXJS               |
| :----------------- | :----------------------------------- | :------------- | :----------------- |
| 17.1.0             | ^18.13.0 \|\| ^20.9.0                | >=5.2.0 <5.4.0 | ^6.5.3 \|\| ^7.4.0 |
| 17.0.x             | ^18.13.0 \|\| ^20.9.0                | >=5.2.0 <5.3.0 | ^6.5.3 \|\| ^7.4.0 |
| 16.1.x \|\| 16.2.x | ^16.14.0 \|\| ^18.10.0               | >=4.9.3 <5.2.0 | ^6.5.3 \|\| ^7.4.0 |
| 16.0.x             | ^16.14.0 \|\| ^18.10.0               | >=4.9.3 <5.1.0 | ^6.5.3 \|\| ^7.4.0 |
| 15.1.x \|\| 15.2.x | ^14.20.0 \|\| ^16.13.0 \|\| ^18.10.0 | >=4.8.2 <5.0.0 | ^6.5.3 \|\| ^7.4.0 |
| 15.0.x             | ^14.20.0 \|\| ^16.13.0 \|\| ^18.10.0 | ~4.8.2         | ^6.5.3 \|\| ^7.4.0 |

### 1.1. 升级 Nodejs

可以参考我的博客[安装并配置 nodejs](https://www.pengtech.net/nodejs/install_and_config_nodejs.html), 进行安装升级和配置.

对于 Angular 17 来说, 建议升级到 v18.13.0 或者 v20.9.0

### 1.2. 升级 Angular 以及 Typescript

升级 Nodejs 后, 需要重新安装配置 angular cli, 此步骤可用参考我的博客[Angular CLI 安装和使用](https://www.pengtech.net/angular/angular2_installation.html), Angular 建议安装最新版本 16.2.10

总结下来也就以下几步:

```bash

# 安装 typescript
npm i -g typescript@5.3.2
# 安装 Angular CLI
npm install -g @angular/cli@17.3.2
# 或者
cnpm install -g @angular/cli@17.3.2

```

## 2. 升级 Angular 到 v17

前面得准备工作完成后, 接下来就是重要得步骤, 升级 Angular 应用程序了.

升级前建议您先到<https://update.angular.io/?l=3&v=16.0-17.0> 阅读一下有哪些更新, 以及官方建议的步骤, 做到心中有数.

另外建议备份程序, 切换到新的分支, 如果不成功, 可以立即回撤.

### 2.1. 升级 angular cli, core 和 cdk

首先运行以下命令, 通常情况下不会成功, 但是会列出一些依赖关系问题, 确定一下依赖关系不存在严重问题, 可以使用--force 选项继续更新.

```bash

ng update @angular/core@17 @angular/cli@17

```

### 2.2. 升級依賴包

升級完 Angular 內核以及 cli 以后，再升级第三方依赖包，例如：

```bash

 "ngx-markdown": "^16.0.0",  ==> "ngx-markdown": "^17.1.1",
 "ngx-owl-carousel-o": "^16.0.0", ==>  "ngx-owl-carousel-o": "^17.0.0",
 "ngx-quill": "^23.0.0", ==> "ngx-quill": "^25.1.2",

```

## 3. 修改 angular.json 配置

### 3.1. 修改 builder

ng build 从单纯编译 client-side application 变成既要编译 client-side 又要编译 server-side application

```diff

       "architect": {
         "build": {
-          "builder": "@angular-devkit/build-angular:browser",
+          "builder": "@angular-devkit/build-angular:application",


```

### 3.2. 修改 polyfills 配置

polyfills 由之前的字符串型变成了数组型

```diff

-            "polyfills": "src/polyfills.ts",
+            "browser": "src/main.ts",
+            "polyfills": [
+              "src/polyfills.ts",
+              "zone.js"
+            ],

```

### 3.3. 修改 PWA 配置

如果项目 enable 了 pwa, 则需要修改 pwa 配置，pwa 由以前的两个属性缩减为了一个属性。

```diff
-            "serviceWorker": true,
-            "ngswConfigPath": "ngsw-config.json"
+            "serviceWorker": "ngsw-config.json"
```

### 3.4. 替换 browser target

```diff

"configurations": {
             "production": {
-              "browserTarget": "angular-demo:build:production"
+              "buildTarget": "angular-demo:build:production"
             },
             "development": {
-              "browserTarget": "angular-demo:build:development"
-            },
+              "buildTarget": "angular-demo:build:development"
+            }

```

### 3.5. 升级 Angular universal

在版本 17 nguniversal 已被移到 Angular CLI repo 中。代码已经被重构和重命名（现在主要在@angular/ssr 下），但核心功能和架构没有改变。

现在 Angular CLI 直接支持服务器端渲染和构建时预渲染等通用功能，不需要与 Universal 单独集成。

如果项目中使用了 Angular universal, 还需要一些手工操作来升级 Angular universal。

#### 3.5.1. 修改 package.json

从 angular v17 开始，@nguniversal 项目已经被整合到 Angular 主项目，所以需要替换相关依赖包，以及修改 ssr 相关配置。

删除@nguniversal/express-engine，引入@angular/ssr

```diff
-    "@nguniversal/express-engine": "16.2.0",
+    "@angular/ssr": "^17.3.2",
```

#### 3.5.2. 修改 angular.json 配置

```bash

    "server": "src/main.server.ts",
    "prerender": true,
    "ssr": {
      "entry": "server.ts"
    }

```

#### 3.5.3. 修改 tsconfig.app.json

```diff

   "files": [
     "src/main.ts",
+    "src/main.server.ts",
+    "server.ts",
     "src/polyfills.ts"
   ],


```

## 4. trouble shooting

### 4.1. issue 1

```bash
Dynamic require is not supported
Error: Cannot find module './landing-page.component-UB6FKJPP.mjs'
```

参考： [Dynamic require is not supported](https://github.com/angular/angular/issues/54542)

解决办法： 删除.browserslist 文件

### 4.2. issue 3

```bash

ReferenceError: localStorage is not defined

```

解决办法参考：

- [Angular 服务器端渲染应用的一个错误消息 localStorage is not defined](https://bbs.huaweicloud.com/blogs/384415)
-

1. 使用 Angular 的依赖注入（Dependency Injection, DI）
   Angular 提供了一种机制，允许你在应用程序中使用浏览器的 API，而不需要直接访问全局对象。你可以使用 Angular 的 DI 系统来注入 localStorage。

```ts
import { Component, Inject, NgModule } from "@angular/core";
import { LOCAL_STORAGE } from "@ng-web-apis/common";

@Component({
  // ...
})
export class SomeComponent {
  constructor(@Inject(LOCAL_STORAGE) private localStorage: Storage) {
    // 现在你可以安全地使用localStorage
    const item = this.localStorage.getItem("key");
  }
}
```

2. 服务器端渲染时使用 UNIVERSAL_LOCAL_STORAGE
   在服务器端渲染时，你需要提供一个 localStorage 的实现。你可以使用@ng-web-apis/universal 包中的 UNIVERSAL_LOCAL_STORAGE 来实现这一点。

```ts
import { NgModule } from "@angular/core";
import { ServerModule } from "@angular/platform-server";
import { AppComponent } from "./app.component";
import { UNIVERSAL_LOCAL_STORAGE } from "@ng-web-apis/universal";

@NgModule({
  imports: [ServerModule /* ...其他模块... */],
  providers: [UNIVERSAL_LOCAL_STORAGE],
  bootstrap: [AppComponent],
})
export class AppServerModule {}
```

3. 条件渲染和自定义指令
   如果你的应用中有某些功能只在客户端运行，你可以使用条件渲染来避免在服务器端渲染这些功能。例如，你可以使用\*ngIf 指令或者创建自定义指令来实现这一点。

```ts
import { Component, Inject, PLATFORM_ID } from '@angular/core';
import { isPlatformServer } from '@angular/common';

@Component({
  selector: 'ram-root',
  template: '<some-comp *ngIf="isServer"></some-comp>',
  styleUrls: ['./app.component.less']
})
export class AppComponent {
  isServer = isPlatformServer(this.platformId);
  constructor(@Inject(PLATFORM_ID) private platformId: Object) {}
}
4. 环境检测和默认值
在某些情况下，你可能需要在代码中检测当前环境是否为服务器端，如果是，则提供一个默认值或者空对象，避免访问localStorage。

let storage = {};
if (typeof localStorage !== 'undefined') {
  storage = localStorage;
} else {
  // 为服务器环境提供默认实现或者空对象
}

// 现在你可以安全地使用storage对象
const item = storage.getItem('key');

```

5. 使用第三方库
   还有一些第三方库可以帮助你处理服务器端渲染时的 localStorage 问题，例如@ngx-webstorage/core 或者 ngx-store.

通过以上方法，你可以有效地解决 Angular 17 中 localStorage is not defined 的问题，确保你的应用在服务器端渲染时也能正常工作。记得在实际部署和开发过程中，根据你的应用需求和架构选择合适的解决方案。

### 4.3. issue 3

```bash
NG02801: Angular detected that `HttpClient` is not configured to use `fetch` APIs. It's strongly recommended to enable `fetch` for applications that use Server-Side Rendering for better performance and compatibility. To enable `fetch`, add the `withFetch()` to the `provideHttpClient()` call at the root of the application.
```

原因参考： <https://stackoverflow.com/questions/77512654/angular-detected-that-httpclient-is-not-configured-to-use-fetch-apis-angul>

解决方案：

修改 app.config.ts,添加 provideHttpClient(withFetch()),

## 5. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 6. 参考文档

[Upgrade to Angular 16 step by step](https://medium.com/@ahmad.g.mustafa/upgrade-to-angular-16-step-by-step-e23d2963a2a8)
