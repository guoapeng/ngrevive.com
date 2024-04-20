---
title: Angular图表库介绍
date: 2023/09/03
tags:
  - javascript
  - web
  - Angular
categories:
  - frontend
reward: true
---

## 1. 前言

如今，数据分析是任何业务应用程序的重要组成部分。这有助于企业做出重要决策。以易于理解和交互的方式表示大量数据非常重要。
图表对于美观、易于理解和交互式的数据可视化非常有用。

JavaScript 中有不同的开源和付费图表库，可以实现漂亮的数据表示。

在本文中，我们将研究几款美观的, 易于交互的 Angular 图表库。

首先，我们将看到开源 Angular 图表库，稍后我们将研究其他付费 Angular 图表库.

<!-- more -->

## 2. npm 趋势图

以下是近五年(as of 2023-09-04)的 npm 趋势图

![Angular chart lib trends](https://www.pengtech.net/images/P3lVCSR.jpg)

以及状态图

![Angular chart lib status](https://www.pengtech.net/images/ioIgaDm.jpg)

从图中我们可以看出, ng2-charts 是比较老牌的 angular 图表库, 而且一直保持着较好的增长. 后起之秀 highcharts-angular 发展也比较迅猛.

ngx-echarts 一直保持着稳步增长. 从活跃度来看 ng2-charts 更新一直比较活跃.

> 注意以上图中, 没有将 PrimeNG/Charts 加入到比较, 因为 PrimeNG/Charts 是 PrimeNG 这个大包里面的一个模块, npmtrends 不支持将模块添加到比较行列.
> 而将整个 PrimeNG 添加到比较行列对其它 charts 库又不太公平, 所以请自行判断 PrimeNG/Charts 在整个 Angular chart libs 中的地位和发展.

## 3. 最佳开源 Angular 图表库

以下是使用 Angular 技术封装的最佳开源图表库

- ng2-charts
- ngx-echarts
- PrimeNG Charts
- ngx-charts
- angular-plotly.js

## 4. ng2-charts

[ng2-charts](https://valor-software.com/ng2-charts/#/GeneralInfo)是经过 Angular 包装的基于[Chart.js](https://www.chartjs.org/)的 Angular 指令(directive)

Chart.js 是一个流行的开源 JavaScript 图表库, Chart.js 使用 HTML5 画布，可在所有现代浏览器（IE11+）上提供出色的渲染性能。

ng2-charts 它提供了便于集成到 Angular 应用程序中的原理图。

### 4.1. Ng2-Charts 功能

图表类型:

ng2-charts 支持 8 种图表类型：

- 折线图
- 条形图
- 雷达图
- 饼图
- 面积图
- 圆环图
- 气泡图
- 散点图

定制化:

- 图例(Legends)
- 标签
- 颜色
- 提示条(Tooltip)
- 主题(Theming)
- 组合图表(Combine Charts)
- 选项(Options)

在这里查看[Angular 13 中带有 ng2-charts 的很棒的图表](https://www.ngdevelop.tech/angular-ng2-charts-develop-awesome-charts-in-angular-13/)

## 5. ngx-charts

[ngx-charts](https://swimlane.github.io/ngx-charts/#/ngx-charts/)是 Angular2+ 的声明式图表框架。

它使用 Angular 来渲染和动画 SVG 元素，并利用 Angular 所有的绑定和速度优势，并使用 d3 来实现出色的数学函数、比例、轴和形状生成器等。

通过让 Angular 完成所有渲染，它为我们带来了 Angular 平台提供的无限可能性，例如 AoT、Universal 等。

ngx-charts 允许我们使用 CSS 自定义样式。我们还可以使用 ngx-charts 组件创建自定义图表。

以下是一篇很好的关于如何在 Angular 项目中使用 Ngx-Charts 的文章[How To Use Ngx-Charts In Angular Application ?](https://www.ngdevelop.tech/how-to-use-ngx-charts-in-angular/)

### 5.1. ngx-charts 功能

- 图表类型

  - 水平和垂直条形图(bar charts)
  - 线型图(line)
  - 面积图(Area)（标准、堆叠、归一化）
  - 饼图(Pie)（可爆炸、网格、自定义图例）
  - 气泡图(bubble)
  - 油炸圈饼(Doughnut)
  - 仪表盘(Gauge)（线性和径向）
  - 热图(Heatmap)
  - 树形图(Treemap)
  - 数字卡片图(Number Cards)

- 定制化
  - 自动缩放
  - 时间线过滤(Timeline Filtering)
  - Line Interpolation
  - 可配置的轴标签
  - 图例(Legends)（标签和渐变）
  - 可定制的标签位置
  - 实时数据支持
  - 可定制的工具提示
  - 数据点事件处理程序(Data point Event Handlers)
  - 应用主题(Theming)
  - 可以配合 ngUpgrade 一起升级

## 6. Ngx-Echarts

[ngx-echarts](https://xieziyu.github.io/ngx-echarts/#/home)是经过 Angular 包装的基于[ECharts](https://echarts.apache.org/examples/en/) (3.x+) 的 Angular 指令(directive)

ECharts 是一个开源的、基于 Web 的、跨平台的框架，支持交互式可视化的快速构建。

ECharts 在 github 上拥有 39.6k star 和 13.2k fork，被视为全球领先的可视化开发工具，在 GitHub 可视化类别中排名第三。

它可以在 PC 和移动设备上流畅运行。它与大多数现代网络浏览器兼容，例如 IE8/9/10/11、Chrome、Firefox、Safari 等。ECharts 依赖图形渲染引擎 ZRender 来创建直观、交互式和高度可定制的图表。

### 6.1. Ngx-Echarts 功能

- 图表类型

  - 线型图系列
  - 条形图系列
  - 散点图系列
  - 饼图
  - 烛台图系列
  - 统计箱线图系列
  - Map 系列
  - 热图系列
  - 方向信息线系列
  - 关系图系列
  - 树状图系列
  - 旭日图系列
  - 多平行系列尺寸数据
  - 漏斗系列
  - 量具系列

- 定制化
  - 加载处理(Loading Handling)
  - 事件处理(Event Handling)
  - 数据实时更新
  - 初始选项(Initial Options)
  - 自动调整大小(Auto Resize)
  - 应用主题
  - 图表连接(Connect Charts)
  - 可拖拽图表
  - 3D 图表

请参阅[ECharts 文档](https://echarts.apache.org/en/option.html#title)以获取更多自定义信息。

在此处查看[使用 NGX-ECHARTS 在 Angular 中使用 ECharts 进行数据可视化](https://www.ngdevelop.tech/data-visualization-with-echarts-in-angular-using-ngx-echarts/) （带有 COVID Tracker 示例应用程序）。

## 7. Angular-Plotly

[Angular-plotly]()是来自 plotly 的 plotly.js Angular 组件。

它支持 Angular 9.x，如果您想与 Angular 8.x 一起使用，请使用版本`angular-plotly.js@1.x`

plotly.js 构建于 d3.js 和 stack.gl 之上，是一个高级声明性图表库。plotly.js 附带 40 多种图表类型，包括科学图表、3D 图表、统计图表、SVG 地图、金融图表等。Plotly.js 在 github 上有 1.12 万颗星和 1.3 万个分支。

### 7.1. Angular-Plotly.Js 的功能

- 基本图表：
  散点图、条形图、折线图、饼图、气泡图、点图、填充面积图、水平条形图、旭日图、桑基图、点云、多图表类型

- 统计图表：
  误差线、箱线图、直方图、二维密度图、平行类别图。

- 金融图表：
  瀑布图、指标、烛台图、漏斗图和漏斗面积图。

- 地图：
  Mapbox 地图图层、Mapbox 密度热图、Choropleth Mapbox、地图上的线条等。

- 3D 图表：
  3D 散点图、带状图、3D 曲面图、3D 网格图等

定制化:

- 下载为 SVG / PNG
- 数据导出
- 事件处理
- 自动调整大小(Auto Resize)
- 滚动(Scroll)
- 缩放(Zoom)
- 筛选(Filter)
- 动效(Animation)
- 分组(Group by)

## 8. primeng/chart

[PrimeNg/Charts](https://primefaces.org/primeng/#/chart/bar)组件基于 Charts.js 2.7.x，这是一个基于开源 HTML5 的图表库。

PrimeNG 是 Angular 的丰富 UI 组件的集合。所有小部件都是开源的，可以在 MIT 许可下免费使用。

### 8.1. PrimeNG/Charts 功能

图表类型:

目前有 6 个选项可供选择；饼图、圆环图、折线图（折线图或水平条形图）、条形图、雷达图和极面积图。

定制化:

- 响应式(Responsive)
- 事件处理
- 标签
- 图例(Legends)
- 提示条(Tooltip)
- 宽度和高度
- 选项（参考 [Chart.js 文档](https://www.chartjs.org/docs/latest/)）

## 9. Angular Google Charts

angular-google-charts 是为 Angular 6 和 7 编写的 Google Charts 库的包装器(wrapper)。

Google 图表工具功能强大、易于使用且免费。

注意：Google Charts 是免费的，但不是开源的。Google 的许可不允许您在您的服务器上托管他们的 JS 文件。因此，如果您是企业并拥有一些敏感数据，Google Charts 可能不是最佳选择。

## 10. Highcharts Angular

Highcharts Angular 是 Angular 的官方 Highcharts 包装器(wrapper)。

Highcharts 是一个基于 SVG 的现代多平台图表库。它拥有丰富的图表集合。

Highcharts 对于非商业用途是免费的，对于商业用途是付费的。

## 11. Angular Fusion Charts

angular-fusioncharts 是 FusionCharts JavaScript 图表库的简单且轻量级的官方 Angular 组件。angular-fusioncharts 使您能够轻松地在 Angular 应用程序中添加 JavaScript 图表。

FusionCharts 是一个 JavaScript 图表库，拥有饼图、柱形图、面积图、折线图、雷达图等图表以及 150 多种其他用于 Web 应用程序的图表。

Fusion Charts 提供商业用途的付费许可。

## 12. 相关文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 13. 总结

在本文中，我们看到了五个最好的开源 Angular 图表库和其他付费 Angular 图表库。本文原文位于[Angular 图表库介绍 | 鹏叔的技术博客](https://www.pengtech.net/angular/angular_chart_solutions.html), 若要获取最近更新请访问原文.

## 14. 参考文档

[Best Angular Chart Libraries](https://www.ngdevelop.tech/best-angular-chart-libraries)
