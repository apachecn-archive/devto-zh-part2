# 使用 Angular 中的 APIs 模型适配器模式

> 原文：<https://dev.to/florimondmanca/consuming-apis-in-angular--the-model-adapter-pattern-3fk5>

<small>*原载于 2018 年 9 月 4 日 blog.florimondmanca.com。*</small>

在之前的一篇文章中，我写了 REST API 设计中的最佳实践。这些大多适用于服务器端。

从 2018 年 1 月开始，我也在用**做**前端开发**，既是爱好也是专业。**

 **今天，我想谈谈架构，并与你分享一个**设计模式**，它帮助我**构建和标准化**我将**REST API**与**角度前端应用**集成的方式。

TL；博士:

> 编写一个模型类，并使用适配器将原始 JSON 数据转换成这种内部表示。

**提示**:你可以在 GitHub 上找到这个帖子的最终代码: [ng-courses](https://github.com/florimondmanca/ng-courses) 。

让我们开始吧！

## 问题

我们到底想解决什么？

如今，前端应用程序通常需要与外部后端服务进行大量交互，以交换、保存和检索数据。

我们今天感兴趣的一个典型例子是**通过请求 REST API** 来检索数据。

这些 API 以给定的**数据格式**返回数据。这种格式**会随着时间的推移而改变**，在我们用**打字稿**构建的 Angular 应用中，这很可能不是最佳格式。

所以，我们试图解决的问题是:

**我们如何将 API 与 Angular frontend app 集成，同时限制更改的影响并充分利用 TypeScript 的功能？**

## 课程订阅系统

作为一个例子，我们将考虑一个**课程订阅系统**，在这个系统中，学生可以申请课程和访问学习材料。

这是我们试图构建的功能的**用户故事**:

"*作为学生，我想查看课程列表，以便注册新课程。*

为此，我们提供了以下 API 端点:

```
GET: /courses/ 
```

Enter fullscreen mode Exit fullscreen mode

它的响应数据(JSON 格式)如下所示:

```
[  {  "id":  1,  "code":  "adv-maths",  "name":  "Advanced Mathematics",  "created":  "2018-08-14T12:09:45"  },  {  "id":  2,  "code":  "cs1",  "name":  "Computer Science I",  "created":  "2018-06-12T18:34:16"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么不干脆`JSON.parse()`？

快速而简单的解决方案是进行 HTTP 调用，在响应体上应用`JSON.parse()`，并在我们的 Angular 组件和 HTML 模板中使用结果`Object`。事实证明，Angular 的 [HttpClient](https://angular.io/guide/http) 可以帮你做到这一点，所以我们甚至不用做太多工作。

事实上，这就是`CourseService`执行这个 HTTP 请求有多简单:

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
    providedIn: 'root'
})
export class CourseService {

    constructor(private http: HttpClient) {}

    list(): Observable<any> {
        const url = 'http://api.myapp.com/courses/';
        return this.http.get(url);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这种方法有几个问题:

*   容易出错的错误:有许多潜在的错误不会在早期被发现，因为我们没有利用 TypeScript 的静态类型。
*   **高耦合**:对 API 数据模式的任何改变都会在整个代码库中冒泡，即使它不会导致功能上的改变(例如重命名字段)。

因此，虽然服务本身易于维护和阅读，但对于我们代码库的其余部分来说，情况肯定不一样。

## 需要适应

那么，如果仅仅使用 JSON 对象是不够的，我们还有什么更好的选择呢？

好了，我们来思考一下上面的两个问题。

首先，代码**容易出错**，因为我们没有利用 TypeScript 的静态类型和 OOP 特性(比如类和接口)来**建模数据**。解决这个问题的一个方法是创建一个特定类的实例(T4 模型 T5)，这样 TypeScript 可以帮助我们使用它。

其次，我们遇到了关于 API 数据格式的高耦合性。这是因为我们没有从其余的应用程序组件中抽象出这种数据格式。要解决这个问题，一种方法是创建一个**内部数据格式**，并在处理 API 响应时映射到这个格式。

就是这样:我们刚刚概念化了**模型-适配器模式**。它由两部分组成:

*   **模型类**让我们保持在类型脚本的范围内，这确实有很多优点。
*   **适配器**作为单一接口接收 API 的数据并构建模型的实例，我们可以在整个应用程序中放心使用。

我发现图表总是有助于理解概念，所以这里有一个给你:

<figure>

[![](img/ef59c61e9c32ad23a6d89732a24bc069.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UnYWPU2j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/ac486986-40d0-4dab-9dee-a9b3f569e6a1.png)

<figcaption>The Model-Adapter pattern, visualised.</figcaption>

</figure>

## 型号-适配器示例

既然我们已经看到了什么是模型适配器模式，我们在我们的课程系统中实现它怎么样？先说车型。

### `Course`模型

这将是一个简单的类型脚本类，没有什么特别的:

```
// app/core/course.model.ts
export class Course {
  constructor(
    public id: number,
    public code: string,
    public name: string,
    public created: Date,
  ) { }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`created`是一个`Date` Javascript 对象，即使 API 端点将它作为`string` (ISO 格式的日期)提供给我们。您已经可以看到适配器潜伏在这里。

现在我们有了模型，让我们开始编写一个实际的 HTTP 请求服务。我们会看看有什么发现。

### 掐灭`CourseService`

因为我们要进行 HTTP 调用，所以让我们首先在`AppModule` :
中导入`HttpClientModule`

```
 // app/app.module.ts
  import { BrowserModule } from '@angular/platform-browser';
  import { NgModule } from '@angular/core';
+ import { HttpClientModule } from '@angular/common/http'; 
  import { AppComponent } from './app.component';

  @NgModule({
    declarations: [
      AppComponent
    ],
    imports: [
      BrowserModule,
+     HttpClientModule,
    ],
    providers: [],
    bootstrap: [AppComponent]
  })
  export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以创建`CourseService`。按照我们的设计，我们将定义一个`list()`方法来返回从`GET: /courses/`端点获得的课程列表。

```
// app/core/course.service.ts
import { Injectable } from '@angular/core';
import { Course } from './course.model';
import { Observable, of } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class CourseService {

  list(): Observable<Course[]> {
    // TODO
    return of([]);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们已经提到的，Angular 的`HttpClientModule`的一个优点是它内置了对 JSON 响应的支持。实际上，`HttpClient`默认情况下会解析 JSON 主体并从中构建一个 JavaScript 对象。所以让我们试着在这里使用它:

```
// app/core/course.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Course } from './course.model';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class CourseService {
  private apiUrl = 'http://api.myapp.com/courses';

  constructor(private http: HttpClient) { }

  list(): Observable<Course[]> {
   return this.http.get(this.apiUrl);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

此时，TypeScript 的编译器将不幸地咆哮出来(这是一个很好的观点！):

<figure>

[![](img/e3c4a48e48bc8ba87832524589c4e304.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YXpf2D1n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://florimondmanca-personal-website.s3.amazonaws.com/media/markdownx/436c8b5c-edd5-41c6-b346-77c713f9942e.png)

<figcaption>TypeScript is not happy… Why?</figcaption>

</figure>

当然啦！我们还没有从检索到的原始数据中构建`Course`实例。相反，我们仍然返回一个`Observable<Object>`。我们可以使用 [RxJS](https://angular.io/guide/rx-library) 的`map`操作符来解决这个问题。我们将把数据数组映射到一个由`Course`对象组成的数组:

```
// app/core/course.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Course } from './course.model';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class CourseService {

  private apiUrl = 'http://api.myapp.com/courses';

  constructor(private http: HttpClient) { }

  list(): Observable<Course[]> {
    return this.http.get(this.apiUrl).pipe(
      map((data: any[]) => data.map((item: any) => new Course(
        item.id,
        item.code,
        item.name,
        new Date(item.created),
      ))),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

唷！这将正常工作，并按预期返回一个`Observable<Course[]>`。

然而，这段代码有几个问题:

*   由于适配代码使服务变得混乱，很难阅读。
*   这是**没有干**(不要重复自己):我们需要在所有其他需要从 API 项构建课程的方法中重新实现这个逻辑。
*   这给开发人员带来了**的高认知负荷**:我们需要参考`course.model.ts`文件来确保我们向`new Course()`提供了正确的参数。

所以，一定有更好的方法

### 回车:适配器

当然，是有的！🎉

这就是**适配器**进入的时候。如果你熟悉四人帮的设计模式，你可能会意识到这里需要一个[桥](https://sourcemaking.com/design_patterns/bridge):

> 将抽象与其实现解耦，这样两者可以独立变化。

这正是我们所需要的(但我们称之为适配器)。

适配器本质上将 API 的对象表示转换为我们的内部表示。

事实上，我们甚至可以为适配器定义一个通用接口，比如:

```
// app/core/adapter.ts
export interface Adapter<T> {
    adapt(item: any): T;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以让我们来建造`CourseAdapter`。它的`adapt()`方法将接受一个单独的课程项目(由 API 返回),并从中构建一个`Course`模型实例。你应该把这个放在哪里？我会推荐模型文件本身里面的**:** 

```
// app/core/course.model.ts
import { Injectable } from '@angular/core';
import { Adapter } from './adapter';

export class Course {
    // ...
}

@Injectable({
    providedIn: 'root'
})
export class CourseAdapter implements Adapter<Course> {

  adapt(item: any): Course {
    return new Course(
      item.id,
      item.code,
      item.name,
      new Date(item.created),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意适配器是一个**可注射的**。这意味着我们将能够使用 Angular 的依赖注入系统，就像任何其他服务一样:将它添加到构造函数中，并立即使用它。

### 重构了`CourseService`

现在我们已经将大部分逻辑抽象到了`CourseAdapter`中，下面是`CourseService`的样子:

```
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Course, CourseAdapter } from './course.model';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class CourseService {

  private apiUrl = 'http://api.myapp.com/courses';

  constructor(
    private http: HttpClient,
    private adapter: CourseAdapter,
  ) { }

  list(): Observable<Course[]> {
    return this.http.get(this.apiUrl).pipe(
      // Adapt each item in the raw data array
      map((data: any[]) => data.map(item => this.adapter.adapt(item))),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，现在:

*   代码更加枯燥:我们将逻辑抽象在一个单独的元素(适配器)中，我们可以随意重用它。
*   结果是`CourseService`比**更具可读性**。
*   **认知负荷降低**因为模型及其适配逻辑保存在同一个文件中。

我也向你承诺过,**模型适配器模式**将帮助**减少 API 和我们的应用程序之间的耦合**。让我们看看如果我们遇到一个…

### 数据格式改变！

API 团队的人对 API 的数据格式进行了修改。下面是一个`course`项目现在的样子:

```
{  "id":  1,  "code":  "adv-maths",  "label":  "Advanced Mathematics",  "created":  "2018-08-14T12:09:45"  } 
```

Enter fullscreen mode Exit fullscreen mode

他们把`name`字段的名字改成了`label`！

以前，无论我们的前端应用程序在哪里使用`name`字段，我们都必须将其更改为`label`。

但是我们现在安全了！因为我们有一个**适配器**，它的作用就是在 API 表示和我们的内部表示之间进行映射，我们可以简单地改变如何获得内部的`name`:

```
@Injectable({
  providedIn: 'root'
})
export class CourseAdapter implements Adapter<Course> {

  adapt(item: any): Course {
    return new Course(
      item.id,
      item.code,
-     item.name, +     item.label,
      new Date(item.created),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**一行变化！**和我们应用程序的其余部分可以像往常一样继续使用`name`字段。太棒了。✨

## 好的原则永远适用

我已经在我的大部分 Angular 项目中使用了**模型适配器模式**，这些项目涉及到 **REST API 交互**，并取得了巨大的成功。

它帮助我**减少耦合**并充分利用**打字稿**的力量。

总而言之，这实际上可以归结为坚持软件工程的良好原则。这里主要的是**单一责任原则** — *每个代码元素应该做一件事，并且做好*。

如果你有兴趣，可以在 GitHub 上找到最终代码: [ng-courses](https://github.com/florimondmanca/ng-courses) 。

我希望这个简单的架构技巧能帮助你**改进在 Angular 应用中集成 API 的方式**。然而，如果你自己用另一种方法成功了，我很想听听这个故事！

* * *

**更新**(2019 . 3 . 1):我写了这篇文章的后续帖子。它解释了如何在角度分量中使用这里创建的`CourseService`:

[![florimondmanca](img/122f3b1f9c33dcdad1433bb20f0dab94.png)](/florimondmanca) [## 在 Angular 中使用 API:在组件中显示数据

### 弗洛里蒙德·曼卡 3 月 1 日 1915 分钟阅读

#webdev #angular #showdev #tutorial](/florimondmanca/consuming-apis-in-angular-displaying-data-in-components-3b97)

## 保持联系！

如果你喜欢这篇文章，你可以在 Twitter 上找到我，获取更新、公告和新闻。🐤****