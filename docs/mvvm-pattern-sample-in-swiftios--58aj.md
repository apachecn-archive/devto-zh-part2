# Swift/iOS 中的 MVVM 模式示例

> 原文：<https://dev.to/eleazar0425/mvvm-pattern-sample-in-swiftios--58aj>

MVVM 是一种越来越受欢迎的模式，而更多面向事件的应用程序正变得越来越流行。在 iOS 开发中使用 MVVM 与经典的 MVC 相比有很多优势，首先是将业务逻辑与表示层完全分开。我们可以将像从网络或本地数据库请求数据这样的事情委托给另一个实体，而不是让一个“大规模视图控制器”负责做太多事情。

[![https://thepracticaldev.s3.amazonaws.com/i/ibcak32czzov3pwvyskp.png](../Images/01b092a0ff22c461ae391ee4ad05e368.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ehhidZCf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ibcak32czzov3pwvyskp.png)

所有这些应用程序逻辑都在 ViewModel 中，它永远不知道视图是什么或者视图做什么。是什么让这个架构变得非常可测试，并从视图中去掉复杂性，让它尽可能的简单。

视图拥有视图模型，并且总是“监听”更新 UI 的变化。这里出现了著名的“双向数据绑定”，如果视图有变化，模型被更新，如果模型有变化，视图被更新，总是有一个作为中介的视图模型。

好吧，如果我们在实践中看到这个呢？想象以下场景:我想开发一个连接 Github 的 API rest 的应用程序，允许我在存储库中搜索，选择其中一个，并查看其最近的提交。我们开始吧！

我将从搜索功能开始，为了简洁起见，我将忽略许多内容，但您可以在最后看到所有源代码。

让我们编写满足我们的视图模型的协议:

```
protocol SearchRepositoriesDelegate {
    func searchResultsDidChanged()
}

protocol SearchViewModelType {
    var results: [SearchResult] {get}

    var query: String {get set}

    var delegate: SearchRepositoriesDelegate? {get set }
} 
```

Enter fullscreen mode Exit fullscreen mode

结果的属性，检索这些结果的查询的属性，以及通知视图已有更改的委托。简单吧？

请注意，在另一个场景中的“SearchRepositoriesDelegate”应该由一些数据绑定机制或实现 observables 替换，但这将是另一篇文章。

那么我们的视图模型将以如下方式工作:

1-视图将引用 ViewModel。当用户在搜索栏中输入时，视图将根据查询更新视图模型的“查询”属性。
3-每次更新“查询”属性时，ViewModel 都会向 Github API rest 发出请求，并更新结果。
4-一旦有服务器响应，ViewModel(通过代理)通知视图结果有变化。
5-每次调用函数‘searchResultsDidChanged’时，视图都会更新用户界面。

我们来看看实现是怎样的:

视图模型:T0

```
 class SearchViewModel : SearchViewModelType {
    var delegate: SearchRepositoriesDelegate?

    var results : [SearchResult] = [] {//0 results by default
        didSet{
            delegate?.searchResultsDidChanged() //notify
        }
    }

    var searchService: SearchService

    var query: String = "" {
        didSet {
            if query == "" {
                results = []
            }else {
                performSearch()
            }
        }
    }

    init(service: SearchService) {
        self.searchService = service
    }

    private func performSearch() {
        searchService.search(query: self.query)
            .onSuccess { results in
                self.results = results
            }.onFailure { error in
                //do nothing
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看:

```
class SearchViewController: UIViewController {

    ...

    var searchViewModel: SearchViewModelType!

    override func viewDidLoad() {
        super.viewDidLoad()

        searchViewModel.delegate = self

        ...
    }
}

extension SearchViewController: UISearchBarDelegate {
    func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
        searchViewModel.query = searchText
    }
}

extension SearchViewController: SearchRepositoriesDelegate {
    func searchResultsDidChanged() {
        self.tableView.reloadData()
    }
}

... 
```

Enter fullscreen mode Exit fullscreen mode

当用户在搜索栏中输入时，属性“query”被更新，ViewModel 请求服务器端。请求完成后，它会通知视图，并调用 UITableView 的“reloadData()”函数来反映 UI 中的更改。正如您可能已经看到的，SearchViewModel 类非常容易测试，因为我们只需要创建一个模拟的“SearchService”对象来检查它是否正常工作。ViewModel 没有对视图的引用，视图不受所有业务逻辑的约束。

仅此而已！不要害怕看到[这个链接](https://github.com/eleazar0425/Recent-Commits-on-Repository)中的所有源代码，并评论任何问题。