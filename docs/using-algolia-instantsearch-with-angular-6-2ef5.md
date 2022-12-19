# 使用 Algolia 即时搜索和角度 6。

> 原文：<https://dev.to/sirbootoo/using-algolia-instantsearch-with-angular-6-2ef5>

嘿，各年龄段的女士们先生们，

这将是我写的第三篇关于 Algolia 的文章。到目前为止，Algolia Instantsearch 和 Angular 的后续工作进展顺利。不，我不是 Algolia 的员工，只是一个热爱他们工作的粉丝。

所以让我们直接进入教程。几周前，Algolia 发布了一个支持 Angular 5 和 6 的更新，相信我，它非常棒，超级简单

我们将从安装 Angular CLI 开始

```
npm install -g angular/cli@6 
```

之后，我们将创建一个名为 ng-algolia 的新项目

```
ng new ng-algolia 
cd ng-algolia 
```

然后，我们进入项目，在此之后，它完成了创建和安装所有必要的包。之后，我们安装角度即时搜索包来完成我们需要做的事情

```
npm install angular-instantsearch --save 
```

安装完成后，我们可以进入代码。为了让这个包工作，我们必须将`NgAisModule`导入到`app/src/app.module.ts`文件中。

```
import { NgAisModule } from 'angular-instantsearch';  
@NgModule({   
     declarations: [AppComponent, ...],   
     imports: [NgAisModule.forRoot(), ...],      
     bootstrap: [AppComponent]                            
}) 
export class AppModule {} 
```

我们将`NgAisModule.forRoot()`添加到 App 模块，因为它是根模块，如果你在一个共享模块中使用它(这意味着任何其他模块不是应用程序的根模块)。)你就要省略了。forRoot()。

Algolia 一直很友好地为我们提供设计。要获得 Algolia 样式，我们必须将以下代码插入到 apps > styles 数组中的 angular.json 中:

```
{
  "styles": [
    "node_modules/angular-instantsearch/bundles/instantsearch.min.css",
    "node_modules/angular-instantsearch/bundles/instantsearch-theme-algolia.min.css",
    "styles.css"
  ]
} 
```

然后，您需要更新名为 polyfills.ts 的文件，将下面的代码添加到代码的底部(即，在所有注释之后😉 ).

```
(window as any).process = {
  env: { DEBUG: undefined },
}; 
```

所以在所有的导入、复制和粘贴之后，我们现在可以使用 Algolia Instantsearch 小部件了(万岁！！！🙌 ).

我记得我说过这很简单，对吗？你现在会明白为什么了。你需要做的一切都可以在你的 html 文件中完成。

首先，我们将从支持与 Algolia 连接的父节点开始。

```
<ais-instantsearch
  [config]="{
    apiKey: '6be0576ff61c053d5f9a3225e2a90f76',
    appId: 'latency',
    indexName: 'instant_search',
    routing: true
  }"
>
</ais-instantsearch> 
```

apikey、appId、IndexName 可以从您的 Algolia 仪表盘中获取。

现在让我们添加搜索框(`<ais-search-box>`)和我们需要的结果列表`<ais-hits>`。

```
<ais-instantsearch [config]="{...}">
  <ais-search-box></ais-search-box>
  <ais-hits></ais-hits>
</ais-instantsearch> 
```

以上将返回您在 Algolia 仪表板中的默认结果格式，要获得个性化和定制的结果，您可以使用:

```
<ais-hits>
  <ng-template let-hits="hits">
    <div *ngFor="let hit of hits">
      Hit {{hit.objectID}}: {{hit.name}}
    </div>
  </ng-template>
</ais-hits> 
```

搜索的一个非常重要的方面是突出显示结果的匹配部分。我们可以使用这个小部件来帮您完成这项工作:

```
<ais-hits>
  <ng-template let-hits="hits">
    <div *ngFor="let hit of hits">
      Hit {{hit.objectID}}:
      <ais-highlight attribute="name" [hit]="hit">
      </ais-highlight>
    </div>
  </ng-template>
</ais-hits> 
```

虽然搜索框是文本搜索的一种方式，但是您可能还希望提供基于记录结构的过滤器。

Algolia 提供了一组参数，用于按面、数字或地理位置进行过滤。Angular InstantSearch 将这些打包成一组小部件。

因为这里使用的数据集是电子商务数据集，所以让我们添加一个 RefinementList 来按类别过滤产品:

将这段代码添加在`</ais-search-box>`之后或您希望它出现在代码中的任何地方。

```
<ais-refinement-list attribute="brand"></ais-refinement-list> 
```

attribute 选项指定在这个小部件中使用的刻面属性。该属性也应该在索引配置中声明为一个方面。

显示的值由 Algolia 根据结果计算得出。

让我们看看完整的代码:

`AppComponent.ts`

```
<ais-instantsearch [config]="{...}">
  <ais-search-box></ais-search-box>
  <ais-refinement-list attribute="brand"></ais-refinement-list>
  <ais-hits>
    <ng-template let-hits="hits">
       <div *ngFor="let hit of hits">
         Hit {{hit.objectID}}:
         <ais-highlight attribute="name" [hit]="hit">
         </ais-highlight>
       </div>
    </ng-template>
  </ais-hits>
</ais-instantsearch> 
```

这是迄今为止我们努力的结果:

到目前为止一切都很好。

是的，在我忘记之前，有一个小挑战，我有解决办法。如果你需要编辑类似搜索框、细化列表或任何 Algolia Instantsearch 组件的样式，你必须使用`::ng-deep`来指向组件中的内部节点。

举例来说，如果你想编辑搜索框输入字段，你必须这样做。

```
::ng-deep .ais-SearchBox-input { background-color: #fff; } 
```

我希望这能帮助你创造出令人惊叹的 Algolia 项目。