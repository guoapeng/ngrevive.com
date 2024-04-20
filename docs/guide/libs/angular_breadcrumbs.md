---
title: 使用Angular实现面包屑导航
date: 2023/12/31
tags: 
- javascript
- web 
- Angular
categories:
- frontend
---

面包屑导航(Breadcrumb Navigation)这个概念来自童话故事“汉赛尔和格莱特”，当汉赛尔和格莱特穿过森林时，不小心迷路了，但是他们发现沿途走过的地方都撒下了面包屑，让这些面包屑来帮助他们找到回家的路。

<!-- more -->

## 面包屑导航的作用

面包屑导航最大的两个主要作用，一是让用户清晰地知道自己在那里，不会在网站中迷失方向，对网站的逻辑结构逐渐形成清晰地认识。尤其是初次通过外链接或者搜索引擎导流行进来的用户。

其二，用户能够快速的在页面之间切换，有人将面包屑导航比喻为电梯，可以在楼层之间自由地切换，相较于一级一级返回“楼梯”式的导航或者从主菜单引导式的导航，面包屑导航根据快捷方便，用户有更强的操控感。

但是面包屑导航不能作为主导航，它仍然只能主导航的辅助手段。就像我们日常饮食还是要以大米面食为主，但是如果有一些餐后甜点和水果，用户的满意度会更高。

对于搜索引擎来讲，面包屑导航会让蜘蛛了解你的网站结构，方便爬取索引。

## 面包屑导航的优点

- 减少不必要的步骤

面包屑导航最实用的一点便是可以帮助用户更快地访问上级网页，无需借助浏览器的“返回”按钮和顶级导航栏。

- 占用空间少

面包屑导航仅由文本和链接组成的一行内容构成，因此占用的页面空间非常小。这样的好处是当内容过载时它的功能也不会受到影响。

- 用户体验很好

用户可能会忽略这个小控件，但是他们从来不会误解或在使用上遇到问题。

## 面包屑导航的分类

面包屑导航大致可以分为三类：

- 一是基于层次结构的导航。
  
  这也是用户最经常用到的，它可以显示出当前页面在整个网站中所占据的层次，类似主页>博客＞分类>文章名称。对于结构层次深的网站来说很有帮助，不至于让用户在网站中迷路，对于用户体验的提升很有帮助；

- 二是基于属性的面包屑导航。
  
  这种导航常见于电商类网站，显示为商品的属性类别，例如进行购买手机的时候可以对手机的品牌、系统、内存及尺寸等方面进行筛选组合，是用户方便了解当前查看的商品的属性；

- 三是基于路径的导航。
  
  与童话中描述的方式相似，记录显示用户所访问过的网页，这种导航井不常见。

## 面包屑导航可用性设计指南

面包屑显示为页面顶部的链接，通常位于全局导航下方；主页（或层次结构的根节点）是第一个链接。

链接通常用符号“>”或“/”分隔，我们推荐使用“>”字符，尽管两者在功能上没有区别。

当用户跳过一些网站层级，比如点击外部链接（如搜索引擎结果到达网站），面包屑会引导并帮助他们找到通往其他更相关页面的路径。

### （一）在桌面上（PC端）使用面包屑导航的指南

1.面包屑不应取代全局导航栏或部分本地导航。

面包屑可以作为导航的有效补充，但是不能取代主要导航。可以采用下拉式菜单。更好的设计是为本地导航提供单独的Ul，以使用户能够访问站点当前部分的横向层级页面。

2.面包屑应该显示站点层次结构中的当前位置，而不是浏览历史记录。

面包屑并不用于显示用户在网站的页面浏览历史记录（例如浏览器的本机后退按钮）；它们旨在显示站点的层次结构。

3.对于多层次站点，面包屑应显示站点多层次结构中的单一路径。

面包屑与多层次站点（其中一个页面有享个父级） 之间存在固有的紧张关系。在这种情况下，我们不建议两个或更多反映多层次结构中不同路径的面包屑路径，因为它们会混淆用户并在页面顶部占用大量空间。

如果一个页面有多个不同的父级，请在站点层次结构中标识到它的规范路径，并在面包屑路径中显示该路径。

4.包括当首页面作为面包屑路径中的最后一顶。

5.在面包屑路径中，当前页面对应的面包屑不应该是链接。

最后一个面包屑（表示当前页面） 不应该是链接。

为避免混淆用户，请在视觉上区分当前页面和前面键接的面包屑，最好使用下划线或蓝色文本。

6.面包屑应送只包含网站页面，而不是IA中的逻辑类别。

面包屑路径中的每个节点都应该是一个指向主页的链接。如果全局导航中的某些子类别标签没有专门的单独页面，请不要在面包屑路径中包含这些子类别。

“点击即走”能力是用户理解面包屑的关键部分，因此所有项目(当前页面除外)都应该代表用户可以去的地方。

7.对于具有仅 1-2层深的扁平结构站点或结构呈线性的站点，面包屑导航是非必需的。

8.面包屑路径应该以指向主页的链接开头。

### （二）在移动设备上使用面包屑的指南

需要提出的是，在移动设备上，使用面包屑的成本很快就会超过收益。

9.不要使用包含多行的面包屑。

在移动网站上，面包屑可以快速换成多行，并在已经拥挤的移动显示器上占用宝贵的空间。多行面包屑路径不能很好地说明链的结构。

10.不要使用太小或太拥挤的面包屑。

一些网站试图通过使链接更小或更靠近来减少面包屑占用的屏幕空间。不幸的是，此解决方案不适用手触摸屏：点击目标至少需要 1cm x 1cm。

11.考虑缩短面包屑路径以仅包含最后一个级别。

在某些页面上，指向一个级别的单个面包屑可能是支持主要用户目标所必需的。此解决方案避免了占用宝贵的移动空间的冗长、拥挤的面包屑路径。

请注意，此建议与准则 #8 冲突，井且只能在移动设备上完成。在桌面上一一有更多空间——总是显示完整的轨迹。

## Angular面包屑导航选型

在Angular项目中添加面包屑导航有很多选择。以下是一些Angular 面包屑导航组件库。

-  ngx-breadcrumbs
-  xng-breadcrumb 
-  [ng-dynamic-breadcrumb](https://github.com/rajaramtt/ng7-dynamic-breadcrumb)
-  [ng2-breadcrumb 82 forks, 102 stars](https://github.com/gmostert/ng2-breadcrumb)
  
ngx-breadcrumbs是基于ng2-breadcrumb的组件库, 并在其基础上增加了对高版本Angular的支持，两者都没有到1.0版本。

ng7-dynamic-breadcrumb是Angular的一个模块，它为应用程序的任何页面生成面包屑。它基于内置的Angular router。但是相比较与xng-breadcrumb更能没有那么丰富。

从[npm趋势](https://npmtrends.com/@exalif/ngx-breadcrumbs-vs-breadcrumbs-vs-ng-breadcrumb-vs-ng-dynamic-breadcrumb-vs-ng2-breadcrumbs-vs-ngx-breadcrumb-vs-xng-breadcrumb)图来看，xng-breadcrumb下载量更高，项目更新更加活跃，更受欢迎。

所以最终还是选择了xng-breadcrumb。

### xng-breadcrumb 特点

✅ 零配置：只需在应用程序的任何位置添加`<xng-breadcrumb></xng-breadscrumb>`。面包屑标签是通过分析应用程序中的角度路线配置自动生成的。
✅ 自定义标签：每条路线都可以通过Angular route Config定义一个自定义标签。生成面包屑时会自动使用这些标签。
✅ 动态更新标签：使用BreadcrummService.set（）动态更改面包屑标签。您可以使用路由路径或路由路径别名来更新标签。
✅ 跳过面包屑：有条件地跳过在面包屑中显示的特定路线。
✅ 禁用breadcrumb：禁用特定路线，以便取消导航到中间路线。
✅ 自定义：自定义面包屑模板以显示带有标签的图标，在文本上使用管道，添加带有ngx-translate的i18n等。
✅ 样式：分隔符和样式可以轻松自定义。
✅ QueryParams和Fragment：通过面包屑导航时保留QueryParams与Fragemnet
✅ SSR：支持nguniversal的服务器端渲染

## 使用xng-breadcrumb快速添加面包屑导航

### 在项目中引入xng-breadcrumb依赖

```bash

npm install --save xng-breadcrumb
//------------- 或者 -------------
yarn add xng-breadcrumb

```

### 在主模块中引入BreadcrumbModule

```ts

import {BreadcrumbModule} from 'xng-breadcrumb';

@NgModule({
  ...
  imports: [BreadcrumbModule],
  ...
})
export class AppModule { }

```

### 在页面中添加xng-breadcrumb组件

```html

<xng-breadcrumb></xng-breadcrumb>

```

完成这些工作。你应该会看到自动生成的面包屑出现在每条路线上。

>注意：xng-breadcrumb对@angular/router有依赖。在app.module.ts导入中需要包括RouterModule（如果您还没有）。


### 定制样式

`<xng-breadcrumb>`为选择器定义了尽可能少的特殊性，以便轻松覆盖它们。

通过更改相应类的CSS来覆盖样式。（如果您不想使用：：ng-deep，请将此样式保存在应用程序根样式文件中）

下面是xng-breadcrumb中涉及的各种类的可视化，以帮助您轻松识别。

![breadcrumb selectors](https://www.pengtech.net/images/angular/breadcrumb_selectors.png)

xng-breadcrumb将“class”作为输入。该类将应用于breadcrumb的根。当风格冲突时，这可以用来增加特异性。

```css

.xng-breadcrumb-root {
  padding: 8px 16px;
  display: inline-block;
  border-radius: 4px;
  background-color: #e7f1f1;
}

.xng-breadcrumb-separator {
  padding: 0 4px;
}

```

## 6. Angular 系列文章

最新更新以及更多 Angular 相关文章请访问 [Angular 合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 参考文档

[Add Breadcrumbs to Your Angular App in Just 5 Minutes](https://betterprogramming.pub/add-breadcrumbs-to-your-angular-app-in-just-5-minutes-3119e376e901)

[Breadcrumbs for Websites: Best Practices & Examples](https://usersnap.com/blog/breadcrumbs/)

[Building Clickable Breadcrumbs Component for Angular Application](https://medium.com/mikael-araya-blog/building-clickable-breadcrumbs-component-for-angular-application-584496378215)

[什么是面包屑导航？](https://zhuanlan.zhihu.com/p/577833955)

[直达电梯－导航的交互设计](https://www.woshipm.com/pd/1462.html)

[你真的会用面包屑导航吗？](https://juejin.cn/post/6844903442889048077)

[Angular and Material Multi-level Menu with Breadcrumb not working properly](https://stackoverflow.com/questions/51132953/angular-and-material-multi-level-menu-with-breadcrumb-not-working-properly)

[xng-breadcrumb](https://udayvunnam.github.io/xng-breadcrumb/)
