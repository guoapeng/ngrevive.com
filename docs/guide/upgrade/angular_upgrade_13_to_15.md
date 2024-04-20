---
title: 从Angular 13升级到Angular 15
date: 2023/06/28
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
reward: true
---

## 1. 前言

升级应用程序或者框架是软件生命周期中非常重要的一项活动. 因为其有风险性, 很多人不愿意去做, 久而久之随着技术债务的积累变成了一件不能去做的事情.

在我的职业生涯中见到过很大这样逐渐失去生命活力的系统, 这里就不具体举例了, 以免引起不必要的争论, 明白的人自然明白.

<!-- more -->

本文最新更新发布于[从 Angular 13 升级到 Angular 15 | 鹏叔的技术博客](https://www.pengtech.net/angular/angular_upgrade_13_to_15.html)

其中的风险, 主要来自新旧版本的不兼容性. 如果兼容旧版本, 无疑是库开发者负责任承担起了这种风险, 但是也导致库变得臃肿, 因循守旧, 开发低效, 逐渐地失去活力. 如果库开发者一路更新向前, 不考虑向前兼容, 库开发走得轻快了, 也该库或者框架的使用者带来了很多创新性的体验, 但是把兼容性风险留给了使用者. 在兼容性, 和创新节奏, 库的活力之间看似鱼和熊掌很难兼得.

而 Angular 在兼容性和变革之间很好地为我们做出了一个榜样, 也是将来版本升级很好的榜样. 首先 Angular 是大胆变革的. 其主版本号没 6 个月就有一次变更, 自从 2016 年发布 Angular 2.x 版本后, 如今一直保持着相似的节奏, 一路升级到 Angular 16.x. 而每次主版本号的变更可能包含了大量的 breaking change 和新特性. 其中有好几个大版本之间差异是很大的. 而 Angular 是如何做到既大胆创新, 又兼顾历史遗留的呢? 那就是 Angular schematics, 它不是简单的代码生成工具. 它也是一个代码重构工具, 这样 Angular 就可以大版本变化是使用一个或多个 schematics 帮助老旧系统自动完成绝大多数的重构工作. 这样旧系统就可以轻松的升级到新版本的 Angular 了. 只要按照 Angular 提供的升级路线, 库或框架使用者就能轻松的完成升级 而 Angular 也不必在新的库中考虑老用户一些传统的习惯和使用方式, 做到轻松上阵.

无独有偶, gitlab 的升级方式也是类似的, 在大版本之间提供 postscript 来弥合新旧版本的差异, 并给使用者提供了清晰的升级路线图. 所以可以预测在将来的开发中, 这种在新旧版本之间使用 postscript 或 Schematics 的方式将逐渐成为一种版本升级的趋势. 开发过库代码的人肯定知道向前兼容是一件多么烧脑和让代码逻辑变得扭曲的事情.

而这种方式带来的坏处就是, 升级变得不像以前那么简单了, 以前升级只需要改个数字, 而现在需要去查升级路线图, 还要执行相应的弥合裂缝的代码. 但是深入思考一下, 这种牺牲还是很值得的, 因为以前开发人员改一下版本号是轻松, 但是将潜在的问题留给了测试人员, 甚至留给了运维人员, 甚至更后, 变得越来越昂贵. 而现在有一条清晰的升级路线, 而这条路线是无数人踩过的, 心里总是会踏实很多.

## 2. 升级前的准备工作

升级前最好阅读一遍 Angular 14, 15 的 release notes. 本文讲述的是从 Angular 13 升级到 Angular 15, 所以需要阅读这两个 release notes: [Angular 14 release notes](https://www.pengtech.net/angular/angular_14_update.html) 和
[Angular 15 release notes](https://www.pengtech.net/angular/angular_15_update.html)

里面详细描述了有那些 breaking change 那些新的弃用, 多我们的迁移工作会非常有帮助.

另外我们需要阅读一下更新指南, 这个更新指南是一个动态的指南, 需要选择从哪个版本升级到哪个版本, 项目中用到了哪些特殊包, 在此基础上系统自动帮我们生成一份更新指南, 非常人性化, 这是 Angular 升级特殊之处, 也是很容易被忽视的一点. 更新指南可以在[这里](https://update.angular.io/)找到

## 3. 开始升级

由于本文的目标是从 Angular 13 升级到 Angular 15, 所以升级路径首先是从 Angular 13 升级到 Angular 14 再从 Angular 14 升级到 Angular 15, 不能一次性升级到 Angular 15.

### 3.1. Angular 13 升级到 Angular 14

#### 3.1.1. 升级前准备工作

阅读 angular 13-14[更新指南](https://update.angular.io/?l=3&v=13.0-14.0)

#### 3.1.2. 步骤一: 更新所有 Angular 的组件到 14

```bash
ng update @angular/core@14 @angular/cli@14 --force
```

注意，不加–force 无法正常升级。
同时，还要事前事后都要 commit 一次。

#### 3.1.3. 步骤二: 升级 Angular Material 到 v14

```bash
ng update @angular/material@14 --force
```

该步骤只适用于使用了 Angular Material 的项目。

#### 3.1.4. 步骤三: 更新 eslint 到 v14

```bash
 ng update @angular-eslint/schematics@14
```

如果项目中使用了 angular-eslint, 可以使用以上命令升级到 v14。

#### 3.1.5. 步骤四：更新一些非 Angular 官方的组件

```bash

npm i @angular/flex-layout@14.0.0-beta.41 ngx-markdown@14.0.1

```

#### 3.1.6. 步骤五：更新 typescript 到 4.6

官方文档说，Angular 14 支持 4.6，没必要安装更新的 Typescript 版本，免得无谓的不兼容问题。

```bash
npm install typescript@4.6.4 -D
```

#### 3.1.7. 步骤六，更新代码

手动解决一些更新指南中的内容.

另外一个问题，如果使用了 moment、lodash 这样的库，会报出一条 warning：

> material-moment-adapter.mjs depends on ‘moment’. CommonJS or AMD dependencies can cause optimization bailouts.

官方文档：[链接](https://angular.io/guide/build#configuring-commonjs-dependencies)
答案是按如下更新 Angular.json

```json
"allowedCommonJsDependencies": [
        "lodash",
        "moment"
 ]
```

#### 3.1.8. 步骤七：启动程序, 手动测试

现在可以启动程序了 npm run start 或者 ng serve, 并进行一些测试和检查.

### 3.2. Angular 14 升级到 Angular 15

#### 3.2.1. 升级前检查一下当前的环境

升级前检查一下当前的环境, 做到心中有数.

```bash

$ng version

     _                      _                 ____ _     ___
    / \   _ __   __ _ _   _| | __ _ _ __     / ___| |   |_ _|
   / △ \ | '_ \ / _` | | | | |/ _` | '__|   | |   | |    | |
  / ___ \| | | | (_| | |_| | | (_| | |      | |___| |___ | |
 /_/   \_\_| |_|\__, |\__,_|_|\__,_|_|       \____|_____|___|
                |___/


Angular CLI: 14.2.0
Node: 18.16.1 (Unsupported)
Package Manager: npm 9.5.1
OS: win32 x64

Angular: 14.2.0
... animations, cdk, cli, common, compiler, compiler-cli, core
... elements, forms, language-service, material
... platform-browser, platform-browser-dynamic, platform-server
... router, service-worker

Package                         Version
---------------------------------------------------------
@angular-devkit/architect       0.1402.1
@angular-devkit/build-angular   14.2.1
@angular-devkit/core            14.2.1
@angular-devkit/schematics      14.2.0
@nguniversal/builders           14.2.3
@nguniversal/express-engine     14.2.3
@schematics/angular             14.2.0
rxjs                            7.4.0
typescript                      4.8.4

Warning: The current version of Node (18.16.1) is not supported by Angular.

```

#### 3.2.2. 升级前准备工作

阅读 angular 14-15[更新指南](https://update.angular.io/?l=3&v=14.0-15.0)

##### 3.2.2.1. 升级 node.js

由于 Angular 15, 不再支持 node.js versions 14.[15-19].x or 16.[10-12].x. [PR #47730](https://github.com/angular/angular/pull/47730)

目前兼容性比较好的是 14.20.x, 16.13.x 或者 18.10.x. 所以需要升级到其中某个版本

Angular, node 以及 typscript 的兼容性可以参考表格 [Actively supported versions](https://angular.io/guide/versions)

升级 Nodejs 可以参考我的博客

[Windows 上安装 nvm 和 nodejs](https://www.pengtech.net/nodejs/install_nvm_on_windows.html)

或者

[安装并配置 nodejs](https://www.pengtech.net/nodejs/install_and_config_nodejs.html)

#### 3.2.3. 升级 typescript

Angular 15 中不再支持 typescript 4.8 以下的版本, 如果 typescript 不是 4.8 及以上版本, 需要升级 typescript
下面以typescript@4.8.4为例

```bash
npm install typescript@4.8.4 -D
```

#### 3.2.4. 步骤一: 更新所有 Angular 的组件到 15

```bash
ng update @angular/core@15 @angular/cli@15 @angular/material@15 --force
```

为了防止一些次要组件不兼容导致主要的升级过程失败, 可以加上--force 选项.

当然如果你想再主要组件升级之前解决所有阻碍升级的问题, 则可以去掉 force 选项.

如果使用到@angular-eslint, 可以使用如下命令更新@angular-eslint 到 v15

```bash
ng update @angular-eslint/schematics@15
```

#### 3.2.5. 步骤三: 升级@nguniversal 版本

如果项目中使用到@nguniversal, 需要将@nguniversal 升级到与 angular 15 匹配的版本

```bash

npm install --save @nguniversal/express-engine@15.2.1
npm install -D @nguniversal/builders@15.2.1

```

完成这些步骤, 基本上应用程序可以编译, 运行! 除了 material 组件没有迁移到 MDC-based 组件, 应用的样式, 行为应该跟前一致.

#### 3.2.6. 步骤二: 迁移 Legacy Angular material 组件到 MDC-based Angular material 组件

因为从 Angular 14 到 15, material design 组件发生了比较大的变化, 所以需要执行这一步骤.

详情请阅读[鹏叔的技术博客 MDC-based Angular Material 组件迁移](https://www.pengtech.net/angular/angular_migrate_to_mdc.html)

如果项目非常大且复杂, Angular Material component 的迁移可以 module by module, 也可以 component by component.

如果项目不是很大, 可以一次性迁移到 MDC-based Angular material

```bash
ng generate @angular/material:mdc-migration
```

这条 migration 指令提供了交互式的选项，根据本身项目的特点进行选择。

#### 3.2.7. 步骤三: 执行测试指令

```bash
ng test
```

在新的版本上对原代码进行编译和测试，有助于升级后的问题发现和解决。

#### 3.2.8. 步骤四：启动程序, 手动测试

现在可以启动程序了 npm run start 或者 ng serve, 并进行一些测试和检查.

```bash
npm run start
# 或者
ng serve --open
```

## 4. 问题排查

### 4.1. Issue 1

升级到 Angular 15 后重新编译, 遇到如下警告

```bash
TypeScript compiler options "target" and "useDefineForClassFields" are set to "ES2022" and "false" respectively by the Angular CLI.
```

分析: 在 Angular 15, typescript 的编译目标是 ES2022, 然后在通过 Babel 再次将 ES2022 编译到.browserslistrc 中定义的最终目标

所以如果 tsconfig.json 中的指定的 target 如果不是 ES2022, 编译器会给出警告.

解决办法: 这里建议升级后将 ts 的 target 修改为 ES2022, 反正最终 js 目标是由.browserslistrc 中的配置决定的.

详细分析请查看, [Typescript target warnings after Angular 15 update](https://stackoverflow.com/questions/75047760/typescript-target-warnings-after-angular-15-update)

### 4.2. Issue 2

在运行 prod 编译的过程中, 遇到如下错误. 如下问题

```bash
ng build --configuration=production && ng run your-app:prerender:production
```

```bash

Error: Optimization error [325.3c5087ba2c9c5885.js]: X [ERROR] Transforming const to the configured target environment ("chrome114.0", "edge114.0", "firefox102.0", "ios10.3", "safari11.0") is not supported yet

    325.3c5087ba2c9c5885.js:5737:6:
      5737 │       const scale = this._cachedMeta.rScale;

```

Angular 15 已经不再支持一些较旧的浏览器版本了, 基本上只能运行 ES5 版本的浏览器都不再支持了.

解决办法: 调整.browserslistrc 的配置, 如果 Angular 15 不支持的浏览器版本, 建议去除掉.

## 5. 相关阅读

本文原文位于[从 Angular 13 升级到 Angular 15](https://www.pengtech.net/angular/angular_upgrade_13_to_15.html), 欢迎访问原文以获得最近更新.
更多 Angular 相关文章请访问[Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 6. 参考文档

[Angular：升级 Angular 13 到 Angular 14](https://blog.csdn.net/alvachien/article/details/127602168)

[Angular: 升级 Angular 14 到 Angular 15](https://blog.csdn.net/alvachien/article/details/129108417)

[Fix broken Angular material legacy styles](https://developapa.com/angular-material-legacy-styles/)

[glitch @material](https://glitch.com/@material)

[Code labs](https://codelabs.developers.google.com)
