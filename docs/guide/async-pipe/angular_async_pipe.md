---
title: Angular Async Pipe详解
date: 2023/09/27
tags: 
- javascript
- Angular
categories:
- frontend
reward: true
---

Async Pipe可以对 Angular 应用程序的更改检测策略产生巨大影响。如果到目前为止您还感到困惑，请详解读完全文。我们一起来了解一下吧！

在 Angular 中，Async Pipe本质上是执行以下三个任务的管道:

- 它订阅一个observable或一个Promise并返回最后发出的值。
- 每当发出新值时，它都会标记组件为需要要检查的。这意味着Angular将在下一个周期中为该组件运行Change Detector。
- 当组件被销毁时，它会取消订阅可观察的内容。

此外，作为最佳实践，建议尝试使用 onPush 更改检测策略上的组件和异步管道来订阅可观察对象。

如果您是Angular的初学者，也许上面对异步管道的解释让人不知所措。因此，在本文中，我们将尝试使用代码示例逐步理解异步管道。只需创建一个新的Angular 项目并继续操作即可；在文章的最后，您应该对异步管道有一些深刻的了解。

<!-- more -->

## 1. 创建服务

让我们从创建产品接口和服务开始。

```ts

export interface IProduct {

     Id : string; 
     Title : string; 
     Price : number; 
     inStock : boolean;

}

```

创建IProduct接口后，接下来在 Angular 服务内部创建一个IProduct数组来执行读写操作。

```ts
import { Injectable } from '@angular/core';
import { IProduct } from './product.entity';

@Injectable({
  providedIn: 'root'
})
export class AppService {

  Products : IProduct[] = [
    {
      Id:"1",
      Title:"Pen",
      Price: 100,
      inStock: true 
    },
    {
      Id:"2",
      Title:"Pencil",
      Price: 200,
      inStock: false 
    },
    {
      Id:"3",
      Title:"Book",
      Price: 500,
      inStock: true 
    }
  ]

  constructor() { }
}
```

请记住，在实际应用程序中，我们从远程API获取数据；然而，在这里我们模仿本地数组中的读取和写入操作，以重点关注异步管道。

为了执行读写操作，我们将 Products 数组包装在 a 中BehaviorSubject，并在每次将新项目推送到数组时发出一个新数组Products。

为此，请在服务中添加代码，如下所示：

```ts

Products$ : BehaviorSubject<IProduct[]>; 
  constructor() {
    this.Products$ = new BehaviorSubject<IProduct[]>(this.Products);
   }
  
   AddProduct(p: IProduct): void{
    this.Products.push(p);
    this.Products$.next(this.Products);
   }

```

让我们看一下代码：

1. BehaviourSubject是一种发出默认值或最后发出值的Subject。我们使用的BehaviorSubject最初发送的默认值是Products数组。
2. 在该AddProduct方法中，我们接收一个Product并将其推送到数组中。
3. 在该AddProduct方法中，将接收到的Product推送到Products数组后，我们将发出更新后的Products数组。

目前，该服务已准备就绪。接下来，我们将创建两个组件: 一个用于添加产品，另一个用于在表格上显示所有产品。

## 2. 添加产品

创建一个名为AddProduct的组件，并添加一个响应式式表单来接受产品信息。

```ts

productForm: FormGroup;
  constructor(private fb: FormBuilder, private appService: AppService) {
    this.productForm = this.fb.group({
      Id: ["", Validators.required],
      Title: ["", Validators.required],
      Price: [],
      inStock: []
    })
  }

```

我们使用FormBuilder来创建FormGroup组件，并在template中使用HTML表单结合productForm来接受用户输入，如下所示：

```html
<form (ngSubmit)='addProduct()' [formGroup]='productForm'>
    <input formControlName='Id' type="text" class="form-control" placeholder="Enter ID" />
    <input formControlName='Title' type="text" class="form-control" placeholder="Enter Title" />
    <input formControlName='Price' type="text" class="form-control" placeholder="Enter Price" />
    <input formControlName='inStock' type="text" class="form-control" placeholder="Enter Stock " />
    <button [disabled]='productForm.invalid' class="btn btn-default">Add Product</button>
</form>
```

在函数AddProduct中，我们将检查表单是否有效。如果有效，我们调用该服务将一种产品推送到Products数组。该AddProduct函数应如下所示：

```ts
addProduct() {
    if (this.productForm.valid) {
      this.appService.AddProduct(this.productForm.value);
    }
  }
```

到目前为止，我们已经创建了一个包含reactive form的组件，用于输入产品信息并调用服务在 Products 数组中插入新产品。如果您使用过 Angular，上面的代码应该很简单。

## 3. 显示产品列表

为了显示产品列表, 我需要这样做:

- 将组件的更改检测策略设置为 Default。
- 在组件中注入AppService。
- 使用 subscribe 方法从 observable 中获取数据。

```ts

@Component({
  selector: 'app-list-products',
  templateUrl: './list-products.component.html',
  styleUrls: ['./list-products.component.css'],
  changeDetection: ChangeDetectionStrategy.Default
})
export class ListProductsComponent implements OnInit, OnDestroy {

  products: IProduct[] = []
  productSubscription?: Subscription
  constructor(private appService: AppService) { }

  productObserver = {
    next: (data: IProduct[]) => { this.products = data; },
    error: (error: any) => { console.log(error) },
    complete: () => { console.log('product stream completed ') }
  }

  ngOnInit(): void {
    this.productSubscription = this.appService.Products$.subscribe(this.productObserver)
  }

  ngOnDestroy(): void {
    if (this.productSubscription) {
      this.productSubscription.unsubscribe();
    }
  }
}

```

让我们看一下代码：

- products变量保存从服务返回的数组。
- 是productSubscription 是RxJS Subscription类型的变量，用于保存从可观察对象的订阅方法返回的订阅。
- 这productObserver是一个具有 next、error 和complete 回调函数的对象。
- 观察者productObserver被传递给 subscribe 方法。
- 在ngOnDestrory()生命周期钩子中，我们取消订阅可观察的内容。

在html模板上，您可以在表格中显示产品，如下所示：

```html

<table>
    <thead>
        <tr>
            <th>Id</th>
            <th>Title</th>
            <th>Price</th>
            <th>inStock</th>
        </tr>
    </thead>
    <tbody>
        <tr *ngFor="let p of products">
            <td>{{p.Id}}</td>
            <td>{{p.Title}}</td>
            <td>{{p.Price}}</td>
            <td>{{p.inStock}}</td>
        </tr>
    </tbody>
</table>

```

## 4. 使用组件

我们将使用这两个组件作为同级组件，如下所示。

```html

<h1>{{title}}</h1>

<app-add-product></app-add-product>

<hr/>
<app-list-products></app-list-products>

```

这里您应该注意的一个关键点是组件AddProduct和ListProducts组件是不相关的。它们之间只有两种方式传递数据：

1. 通过父组件进行通信
2. 通过使用服务进行通信

我们已经创建了一项服务，并将使用该服务在这两个组件之间传递产品信息。

## 5. 运行应用程序

您可以通过单击“添加产品”按钮来添加产品。这会调用服务中的一个函数，该函数会更新数组并从可观察对象中发出更新后的数组。

列出产品的组件会订阅可观察的内容，因此每当我们添加另一个项目时，表就会更新。到目前为止，一切都很好。

## 6. 使用 onPush 变化检测策略

如果您还记得ListProducts组件更改检测策略设置为默认值。现在让我们继续将策略更改为onPush：

```ts

@Component({
  selector: 'app-list-products',
  templateUrl: './list-products.component.html',
  styleUrls: ['./list-products.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ListProductsComponent implements OnInit, OnDestroy {

```

再次，继续运行该应用程序。你发现了什么？正如您注意到的那样，当您用AddProduct组件添加产品时，它会被添加到数组中，甚至更新的数组也会从服务中发出。尽管如此，该 ListProducts组件仍未更新。发生这种情况是因为ListProducts组件的更改检测策略设置为onPush。

**将更改检测策略更改为 onPush 可防止表被新产品刷新。**

对于具有onPush更改检测策略的组件，Angular仅在新引用传递给组件时才运行更改检测器。但是，当observable发出新元素时，它仍然是原来的引用。因此，Angular没有运行变更检测器，并且更新的 Products 数组也没有显示在组件中。

您可以在此处了解有关[Angular变化检测器](https://www.telerik.com/blogs/simplifying-angular-change-detection)的更多信息。

## 7. 我们该如何解决这个问题？

我们可以通过手动调用更改检测器来解决此问题。为此，请注入ChangeDetectorRef组件并调用该markForCheck()方法。

```ts
export class ListProductsComponent implements OnInit, OnDestroy {

  products: IProduct[] = []
  productSubscription?: Subscription
  constructor(private appService: AppService, 
    private cd: ChangeDetectorRef) {

   }

  productObserver = {
    next: (data: IProduct[]) => {
       this.products = data; 
      this.cd.markForCheck(); 
    },
    error: (error: any) => { console.log(error) },
    complete: () => { console.log('product stream completed ') }
  }
  ngOnInit(): void {
    this.productSubscription = this.appService.Products$.subscribe(this.productObserver)
  }

  ngOnDestroy(): void {
    if (this.productSubscription) {
      this.productSubscription.unsubscribe();
    }
  }
}
```

至此，我们完成了以下任务：

我们将 Angular ChangeDetectorRef 注入到组件中。

该markForCheck()方法将该组件及其所有父组件标记为dirty组件，以便 Angular 在下一个更改检测周期中检查更改。

现在运行应用程序，您应该能够看到更新的产品数组。

## 8. Subscribe方式分析

正如您所看到的，在设置为 的组件中onPush，要使用可观察量，请按照以下步骤操作。

- 订阅可观察的内容。
- 手动运行更改检测。
- 取消订阅可观察的内容。

该方法的优点subscribe()是：

- 属性可以在模板中的多个位置使用。
- 属性可以用在组件类的不同位置。
- 您可以在订阅可观察对象时运行自定义业务逻辑。

一些缺点是：

- 对于onPush变更检测策略，您必须手动标记组件以使用该markForCheck方法运行变更检测器。
- 您必须明确取消订阅可观察量。
- 当组件中使用许多可观察量时，这种方法可能会失控。如果我们错过取消订阅任何可观察的内容，则可能存在潜在的内存泄漏等。

使用Async Pipe可以解决上述问题。

## 9. Async管道

异步管道是在组件中处理可观察对象的更好且更推荐的方式。在底层，异步管道执行以下三项任务：

- 它订阅可观察对象发出的最后值。
- 当发出新值时，它标记要检查更改的组件。
- 当组件被销毁时，异步管道会自动取消订阅，以避免潜在的内存泄漏。

所以基本上，异步管道会完成您为订阅方法手动执行的所有三项任务。

让我们修改ListProducts组件以使用异步管道。

```ts
@Component({
  selector: 'app-list-products',
  templateUrl: './list-products.component.html',
  styleUrls: ['./list-products.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ListProductsComponent implements OnInit {

  products?: Observable<IProduct[]>;
  constructor(private appService: AppService) {}
  ngOnInit(): void {
    this.products = this.appService.Products$;
  }
}
```

我们删除了ListProductsComponent之前的所有代码，并将服务返回的可观察赋给产品变量。在现在HTML模板上，使用异步管道。

```html
<table>
    <thead>
        <tr>
            <th>Id</th>
            <th>Title</th>
            <th>Price</th>
            <th>inStock</th>
        </tr>
    </thead>
    <tbody>
        <tr *ngFor="let p of products | async">
            <td>{{p.Id}}</td>
            <td>{{p.Title}}</td>
            <td>{{p.Price}}</td>
            <td>{{p.inStock}}</td>
        </tr>
    </tbody>
</table>
```

使用异步管道可以使代码更清晰，并且您不需要为onPush更改检测策略手动运行更改检测器。在应用程序上，您会看到ListProducts每当添加新产品时组件都会重新。

始终建议的最佳做法是：

- 将组件变化检测策略设置为onPush
- 使用异步管道来处理可观察量

我希望您觉得这篇文章很有用，并且现在已经准备好在您的 Angular 项目中使用异步管道。

## 10. 如何处理异常

如果程序在抓取数据时出现异常, 此时我们可以在 tap算子中捕捉异常, 在catchError算子中将error转换成空数组. 在页面向用户显示异常信息.

```ts

@Component({
  selector: 'app-list-products',
  templateUrl: './list-products.component.html',
  styleUrls: ['./list-products.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ListProductsComponent implements OnInit {

  products?: Observable<IProduct[]>;
  error: Error | null = null;
  constructor(private appService: AppService) {}
  ngOnInit(): void {
    this.products = this.appService.Products$.pipe(
    tap({
      error: (error) => this.error = error
    })
    catchError((err) => of([]))
  );
  }
}
```

在页面检测是否有错误发生, 将错误信息显示给用户.

```html
 <div *ngIf="error" class="error">{{error}}</div>
<table>
    <thead>
        <tr>
            <th>Id</th>
            <th>Title</th>
            <th>Price</th>
            <th>inStock</th>
        </tr>
    </thead>
    <tbody>
        <tr *ngFor="let p of products | async">
            <td>{{p.Id}}</td>
            <td>{{p.Title}}</td>
            <td>{{p.Price}}</td>
            <td>{{p.inStock}}</td>
        </tr>
    </tbody>
</table>
```

## 11. Angular 系列文章

最新更新以及更多Angular相关文章请访问 [Angular合集 | 鹏叔的技术博客](https://www.pengtech.net/angular/)

## 12. 参考文档

[Angular Basics: Step-by-Step Understanding the Async Pipe](https://www.telerik.com/blogs/angular-basics-step-by-step-understanding-async-pipe)

[How to Handle Errors Reactively when Using the Async Pipe](https://eliteionic.com/tutorials/handle-errors-reactively-when-using-async-pipe/)
