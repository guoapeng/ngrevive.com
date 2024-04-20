---
title: 如何在Angular应用中使用Ngx-charts？
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

图表帮助我们以易于理解和交互的方式可视化大量数据。在 Angular 中，我们有各种图表库来创建图表。NGX-charts 就是其中之一。

[ngx-charts](https://swimlane.github.io/ngx-charts/#/ngx-charts/) 是 Angular2+ 的开源声明式图表框架。它由[Swimlane](https://swimlane.com/)维护 。

<!-- more -->

它使用 Angular 来渲染和动画 SVG 元素，并利用其所有的绑定和速度优势，并使用 d3 来实现出色的数学函数、比例、轴和形状生成器等。

通过让 Angular 完成所有渲染，它为我们带来了 Angular 平台提供的无限可能性，例如 AoT、Universal 等。

ngx-charts 支持各种图表类型，如条形图、折线图、面积图、饼图、气泡图、圆环图、仪表图、热图、树状图和数字卡片图。

它还支持自动缩放、时间线过滤、line interpolation、可配置轴、图例、实时数据支持等功能。

在本文中，我们将看到使用 Ngx-Charts 进行数据可视化以及如何在 Angx-Charts 中使用 Angx-Charts

我们会学到，

- 如何在 Angular 中安装 ngx-charts ？
- 创建垂直条形图
- 创建饼图、高级饼图和饼图网格

## 2. Ngx-Charts 安装

- 使用以下命令创建一个新的角度应用程序

  ```bash
    ng new ngx-charts-demo
  ```

- 使用以下命令在角度应用程序中安装 ngx-charts 包。

  ```bash
    # ngx-charts依赖@angular/cdk, 所以需要先安装@angular/cdk, 再安装@swimlane/ngx-charts
    npm install @angular/cdk --save
    npm install @swimlane/ngx-charts --save
  ```

- 导入 NgxChartsModule( from ngx-charts)模块到 AppModule 中
- ngx-charts 还需要 BrowserAnimationsModule。将其导入 AppModule 中

所以我们的 AppModule 最终看起来像这样：

```ts
import { BrowserModule } from "@angular/platform-browser";
import { NgModule } from "@angular/core";
import { AppComponent } from "./app.component";
import { NgxChartsModule } from "@swimlane/ngx-charts";
import { BrowserAnimationsModule } from "@angular/platform-browser/animations";

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, BrowserAnimationsModule, NgxChartsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

安装步骤完成。现在让我们使用以下方法开发各种图表了。

## 3. 垂直条形图(Vertical Bar Chart)

在制作条形图之前我们需要准备一些数据, 当然在示例中这些数据是静态的, 在实际开发过程中我们可通过 restful API 从后台获取数据.
在本例中我们准备了一些简单的销售数据。我们将使用这些数据来生成各种图表。

```ts
import { Component } from "@angular/core";

@Component({
  selector: "app-root",
  templateUrl: "./app.component.html",
  styleUrls: ["./app.component.css"],
})
export class AppComponent {
  saleData = [
    { name: "Mobiles", value: 105000 },
    { name: "Laptop", value: 55000 },
    { name: "AC", value: 15000 },
    { name: "Headset", value: 150000 },
    { name: "Fridge", value: 20000 },
  ];
}
```

要生成垂直条形图，ngx-charts 提供 ngx-charts-bar-vertical 组件，将其添加到 html 模板上，如下所示：

```html
<ngx-charts-bar-vertical
  [view]="[1000,400]"
  [results]="saleData"
  [xAxisLabel]="'Products'"
  [legendTitle]="'Product Sale Chart'"
  [yAxisLabel]="'Sale'"
  [legend]="true"
  [showXAxisLabel]="true"
  [showYAxisLabel]="true"
  [xAxis]="true"
  [yAxis]="true"
  [gradient]="true"
>
</ngx-charts-bar-vertical>
```

ngx-charts-bar-vertical 组件的一些重要属性说明:

- results: 要呈现 salesData 图表，我们需要将此数据对象分配给 results
- view: 设置图表视图的宽度和高度
- xAxisLabel: x 轴标签
- legendTitle: 图例标题
- legend : 如果要显示图例，请将其设置为 true，默认为 false
- showXAxisLabel : 设置 true 以显示 x 轴标签
- showYAxisLabel: 设置 true 以显示 y 轴标签。
- xAxis / yAxis : 设置 true 以显示特定数轴
- gradient: 将其设置为 true 以显示具有渐变背景

## 4. 饼图(Pie Chart)

我们可以使用 ngx-charts-pie-chart 组件生成饼图。将其添加到 html 模板中，如下所示。

```html
<ngx-charts-pie-chart
  [results]="saleData"
  [legend]="true"
  [legendTitle]="'Product Sale Report'"
  [view]="[1000,300]"
  [labels]="true"
>
</ngx-charts-pie-chart>
```

## 5. Advanced 饼图(Advanced Pie Chart)

我们可以使用 ngx-charts-advanced-pie-chart 组件制作高级饼图如下:

```html
<ngx-charts-advanced-pie-chart [results]="saleData" [gradient]="true">
</ngx-charts-advanced-pie-chart>
```

## 6. 饼图网格

我们可以使用 ngx-charts-pie-grid 组件制作饼图网格如下:

```html
<ngx-charts-pie-grid [results]="saleData"> </ngx-charts-pie-grid>
```

我们已经使用 ngx-charts 制作了四种图表

## 7. 最终代码

示例代码可以在[github - ngx-charts-demo](https://github.com/ngdevelop-tech/ngx-charts-demo)上找到

## 6. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 9. 参考文档

[ngx-charts 官方文档](https://swimlane.gitbooks.io/ngx-charts/content/)

[How To Use Ngx-Charts In Angular Application ?](https://www.ngdevelop.tech/how-to-use-ngx-charts-in-angular)
