---
title: 一步一步升级到Angular 16
date: 2023/11/08
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
---

Angular 16 于 2023 年 5 月 3 日发布.

今天，我计划向您介绍如何升级到 Angular 16。

<!-- more -->

## 1. 升级前准备

升级前到 Angular 16 之前, 请确保当前 Angular 已经升级到 Angular 15, 并完成了 Angular 15 所有的迁移任务. 如果没有完成 Angular 13 到 14, 以及 14 到 15 的升级任务, 可以参考我的博客[从 Angular 13 升级到 Angular 15](https://www.pengtech.net/angular/angular_upgrade_13_to_15.html)

升级前需要了解 Angular 16 与 Typescript, nodejs 等的版本兼容信息. 可以参考[Actively supported versions](https://angular.io/guide/versions)

Actively supported versions
| ANGULAR | NODE.JS | TYPESCRIPT | RXJS |
| :--- | :--- | :--- | :--- |
| 16.1.x \|\| 16.2.x | ^16.14.0 \|\| ^18.10.0 | >=4.9.3 <5.2.0 | ^6.5.3 \|\| ^7.4.0 |
| 16.0.x | ^16.14.0 \|\| ^18.10.0 | >=4.9.3 <5.1.0 | ^6.5.3 \|\| ^7.4.0 |
| 15.1.x \|\| 15.2.x | ^14.20.0 \|\| ^16.13.0 \|\| ^18.10.0 | >=4.8.2 <5.0.0 | ^6.5.3 \|\| ^7.4.0 |
| 15.0.x | ^14.20.0 \|\| ^16.13.0 \|\| ^18.10.0 | ~4.8.2 | ^6.5.3 \|\| ^7.4.0 |
| 14.2.x \|\| 14.3.x | ^14.15.0 \|\| ^16.10.0 | >=4.6.2 <4.9.0 | ^6.5.3 \|\| ^7.4.0 |
| 14.0.x \|\| 14.1.x | ^14.15.0 \|\| ^16.10.0 | >=4.6.2 <4.8.0 | ^6.5.3 \|\| ^7.4.0 |

### 1.1. 升级 Nodejs

可以参考我的博客[安装并配置 nodejs](https://www.pengtech.net/nodejs/install_and_config_nodejs.html), 进行安装升级和配置.

对于 Angular 16 来说, 建议升级到 v16.14.2 或者 v18.10.0

### 1.2. 升级 Angular 以及 Typescript

升级 Nodejs 后, 需要重新安装配置 angular cli, 此步骤可用参考我的博客[Angular CLI 安装和使用](https://www.pengtech.net/angular/angular2_installation.html), Angular 建议安装最新版本 16.2.10

总结下来也就以下几步:

```bash

# 安装 typescript
npm i -g typescript@4.9.3
# 安装 Angular CLI
npm install -g @angular/cli@16.2.10
# 或者
cnpm install -g @angular/cli@16.2.10

```

## 2. 升级 Angular 到 v16

前面得准备工作完成后, 接下来就是重要得步骤, 升级 Angular 应用程序了.

升级前建议您先到<https://update.angular.io/?l=3&v=15.0-16.0> 阅读一下有哪些更新, 以及官方建议的步骤, 做到心中有数.

另外建议备份程序, 切换到新的分支, 如果不成功, 可以立即回撤.

### 2.1. 升级 angular cli, core 和 cdk

首先运行以下命令, 通常情况下不会成功, 但是会列出一些依赖关系问题, 确定一下依赖关系不存在严重问题, 可以使用--force 选项继续更新.

```bash

ng update @angular/core@16.2.10 @angular/cdk@16.2.10 @angular/cli@16.2.10

```

然后先升级 angular cli, core

```bash

ng update @angular/core@16.2.10  @angular/cli@16.2.10 --force

```

再升级 CDK

```bash

ng update @angular/cdk@16.2.10

```

如果项目中使用了 angular material, 需要将其升级到对应的版本

```bash

ng update  @angular/material@16.2.10

```

## 3. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 4. 参考文档

[Upgrade to Angular 16 step by step](https://medium.com/@ahmad.g.mustafa/upgrade-to-angular-16-step-by-step-e23d2963a2a8)
