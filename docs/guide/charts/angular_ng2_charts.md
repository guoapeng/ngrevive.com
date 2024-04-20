---
title: 如何在Angular应用中使用Ng2-charts
date: 2023/09/04
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
reward: true
---

## 1. 前言

图表帮助我们以易于理解和交互的方式可视化大量数据。

在 Angular 中，我们有各种图表库来创建图表。

在本文中，我们将使用 Ng2-Charts 在 Angular 中开发出色的图表。

在本文中我们将会讲述

- ng2-charts 介绍
- 在 Angular 中安装 ng2-charts 的两种不同方法。
- 创建条形图
- 创建折线图

<!-- more -->

## 2. ng2-charts 介绍

[ng2-charts](https://www.npmjs.com/package/ng2-charts)是一个基于[chart.js](https://www.chartjs.org/)的开源图表库。

Chart.js 是一个流行的 JavaScript 图表库。ng2-charts 是 Chart.js 的二次封装。它提供基础图表指令用于渲染图表。

ng2-charts 拥有约 2K 的 GitHub Stars，npm 上的每月下载量约为 76.7 万。

它支持以下图表类型：

- 折线图
- 饼图
- 条形图
- 圆环图
- 雷达图
- 极地面积图
- 气泡图
- 散点图

还提供许多自定义选项，例如：

- 反应能力
- 动效定制
- 图表标题
- 图例
- 标签
- 颜色
- 提示条
- 主题
- 组合图表
- 选项

> ng2-charts 现在还提供 add 和 generate schematics 用于安装 ng2-charts 和生成上述支持图表。

## 3. 安装 ng2-charts

使用以下命令创建一个新的 Angular 应用程序.

```bash

ng new angular-ng2-charts-demo

```

我们有两种方法在 Angular 应用程序中安装 ng2-charts。

- 使用 `ng add` schematic
- 手动安装

安装前需要确认 ng2-charts 与 Angular 各版本的兼容关系, 参考[ng2-charts 官方文档](https://github.com/valor-software/ng2-charts)

| Angular version | ng2-chart v1.x | v2.x | v3.x | v4.x | v5.x |
| --------------- | -------------- | ---- | ---- | ---- | ---- |
| 2 - 9           | ✓              |      |      |      |      |
| 10              |                | ✓    |      |      |      |
| 11              |                | ✓    |      |      |      |
| 12              |                | ✓    |      |      |      |
| 13              |                |      | ✓    |      |      |
| 14              |                |      | ✓    | ✓    |      |
| 15              |                |      | ✓    | ✓    |      |
| 16              |                |      |      |      | ✓    |

### 3.1. 使用 ng add schematic 安装 Ng2-Charts

这是在 Angular 中安装 ng2-charts 的简单方法。执行以下命令`ng add` schematic 命令.

```bash

ng add ng2-charts

# 或者安装指定版本的ng2-charts

ng add ng2-charts@x.y.z


```

这个命令，

- 自动安装 ng2-charts 和 chart.js 图表库
- 导入并添加 NgChartsModule 模块到 app.module.ts。

> 如果出现 Cannot find module '@angular/cdk/schematics', 则需要安装 安装@angular/cdk.
> 使用以下命令安装@angular/cdk `npm install --save @angular/cdk`

### 3.2. 手动安装 Ng2-Charts

安装 ng2-charts 和 chart.js 使用以下命令进行打包.

关于 ng2-charts 和 chart.js 的兼容关系, 可以在确定 ng2-charts 的版本后, 打开 ng2-charts 对应版本的 package.json 文件查看.

例如对于 v4.1.1 版本的 ng2-charts, 打开<https://github.com/valor-software/ng2-charts/blob/v4.1.1/package.json>, 我们会发现其对应的 chart.js 版本为 4.0.1

```bash
npm install ng2-charts --save
npm install chart.js --save

# 或者安装特定版本的ng2-charts
npm install ng2-charts@x.y.z --save
npm install chart.js@a.b.c --save

```

导入 NgChartsModule 模块在应用程序主模块中。

我们的最终应用程序模块看起来像：

```ts
import { NgModule } from "@angular/core";
import { BrowserModule } from "@angular/platform-browser";

import { AppComponent } from "./app.component";
import { NgChartsModule } from "ng2-charts";

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, NgChartsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

安装步骤完成。现在让我们看看如何使用 ng2-charts 开发图表。我们将创建一个条形图来显示每月销售数据。

## 4. 使用 Ng2-Charts 创建条形图

ng2-charts 对所有图表类型都有一个指令：baseChart。我们将在画布上使用此指令来渲染图表。

我们首先准备图表数据.

### 4.1. 准备图表数据

我们将创建如下图表数据，在演示中我使用静态数据, 在实际开发过程中我们可以动态的从后端获取数据。

```ts
salesData: ChartData<"bar"> = {
  labels: ["Jan", "Feb", "Mar", "Apr", "May"],
  datasets: [
    { label: "Mobiles", data: [1000, 1200, 1050, 2000, 500] },
    { label: "Laptop", data: [200, 100, 400, 50, 90] },
    { label: "AC", data: [500, 400, 350, 450, 650] },
    { label: "Headset", data: [1200, 1500, 1020, 1600, 900] },
  ],
};
```

labels : x 轴标签。对于线形态, 柱状图, 雷达图来说这是必要的。对于 polarArea,饼图和甜甜圈饼图, 当鼠标悬停在图形上是会显示 label。Label 可以是单个字符串，也可以是表示多行标签的 string[]，其中每个数组元素都位于新行上。

datasets: 数据集是多维数组。每个维度代表不同的数据集。这里标签显示为图例，值将映射到相应的 x 轴标签。

### 4.2. 图表选项

我们可以使用各种图表选项来自定义图表。

```ts
chartOptions: ChartOptions = {
  responsive: true,
  plugins: {
    title: {
      display: true,
      text: "Monthly Sales Data",
    },
  },
};
```

还有许多其他可用的自定义选项。在[Chart.JS 文档](https://www.chartjs.org/docs/latest/)中查看更多选项。

### 4.3. 在 html 模板上添加图表画布

现在要在页面上呈现图表，我们必须添加`<canvas>`在模板上。我们将使用 baseChart 如下。

```html
<canvas baseChart [data]="salesData" [type]="'bar'" [options]="chartOptions">
</canvas>
```

baseChart 的一些重要的属性

type: 表示图表类型，可以是：线、条形图、雷达、饼图、极地区域、甜甜圈
data: 表示图表数据对象.
options: 表示图表选项对象.

其他附加属性：

labels: 除了在数据集中指定 x 轴标签外, 我们还可以使用 labels 属性创建一个单独的字符串数组并将其指定为签标属性.

datasets: 除了在 ChartData 中指定数据集外, 我们可以通过 datasets 将该单独的数组作为数据集传递.

colors: 数据颜色，如果未指定，将使用默认和|或随机颜色。.

生成的图表效果如下:

![ng2-charts bar chart](https://www.pengtech.net/images/nzHSquT.jpg)

## 5. 使用 Ng2-Charts 创建折线图

与条形图相同，我们可以生成折线图。

为了创建平滑的曲线折线图，我们将添加一个附加属性 tension: 0.5 在数据集中。

```ts
salesData: ChartData<"line"> = {
  labels: ["Jan", "Feb", "Mar", "Apr", "May"],
  datasets: [
    { label: "Mobiles", data: [1000, 1200, 1050, 2000, 500], tension: 0.5 },
    { label: "Laptop", data: [200, 100, 400, 50, 90], tension: 0.5 },
    { label: "AC", data: [500, 400, 350, 450, 650], tension: 0.5 },
    { label: "Headset", data: [1200, 1500, 1020, 1600, 900], tension: 0.5 },
  ],
};
```

```html
<canvas baseChart [data]="salesData" [type]="'line'" [options]="chartOptions">
</canvas>
```

生成的图表效果如下:

![ng2-charts line chart](https://www.pengtech.net/images/JNVJ3YR.jpg)

## 6. 源代码

本文中的源代码位于 [github - angular-ng2-charts-demo](https://github.com/ngdevelop-tech/angular-ng2-charts-demo)

## 6. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 8. 参考文档

[Awesome Charts In Angular 13 With Ng2-Charts](https://www.ngdevelop.tech/angular-ng2-charts-develop-awesome-charts-in-angular-13/)
