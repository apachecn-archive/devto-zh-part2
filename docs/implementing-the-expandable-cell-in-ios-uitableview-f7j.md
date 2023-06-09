# 在 iOS UITableView 中实现可扩展单元格

> 原文：<https://dev.to/lawgimenez/implementing-the-expandable-cell-in-ios-uitableview-f7j>

我将在下面实现如何创建可扩展的 UITableView 单元格，例如在 iOS 股票日历上。

[![1-K0Xsn4n9Lx2lqpUDNO-xXA](img/9370d46d500a52adea9ea0785a9b48bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_nvWVlYl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://lawgimenez.me/conteimg/2018/07/1-K0Xsn4n9Lx2lqpUDNO-xXA.gif)

从头开始，在 Xcode 上创建一个新项目。在你的 **main.storyboard** 中删除默认的 ViewController(也删除项目浏览器中多余的未使用的 **ViewController.swift** 文件)并拖动一个新的 TableViewController。

[![1-ti0Kq9gWuhSdxOPXvRjDDw](img/0b3188d2961108df034ccdbd083811d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hf3fAMOy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lawgimenez.me/conteimg/2018/07/1-ti0Kq9gWuhSdxOPXvRjDDw.png)

现在我们已经在故事板上设置了 TableViewController。让我们创建一个新的 Swift 文件，例如将其命名为**formtableviewcontroller . Swift**。并将 is 设置为 TableViewController 中的自定义类。

[![1-XPDBMIMfwL7hBdvehkUnjQ](img/b08039861867981baaf27b81dcf9e8e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--u2pL5ARr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lawgimenez.me/conteimg/2018/07/1-XPDBMIMfwL7hBdvehkUnjQ.png)

[![1-366I_Aw3939Z5uXtdVX0fg](img/81e96985be015c358a187a817ac7ffe9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l5y-5BVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lawgimenez.me/conteimg/2018/07/1-366I_Aw3939Z5uXtdVX0fg.png)

现在我们都准备好了。我们将把 TableView 的内容从动态原型改为静态单元格。

将它设置为静态单元格后，您的 TableView 将默认创建 3 个静态单元格。

对于本演示，我们将使用多少个单元并不重要。

这个的秘密是对细胞的正确测量。单击第一个单元格，并将行高更改为 250。我们的计划是，在牢房里我们有两个视角。高度为 50 的 Label 和高度为 200 的 DatePicker。一共 250。在故事板上，在第一个单元格上拖动一个标签和日期选择器，设置它的约束，并嵌入带有 NavigationController 的 TableViewController，它应该看起来像下图。

[![1-VOvLjMRH2f8Gyu7LmfFvYA](img/deb9416ef0f4ea4e0bb54f31f523bfa8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jhyPrUF7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lawgimenez.me/conteimg/2018/07/1-VOvLjMRH2f8Gyu7LmfFvYA.png)

现在让我们开始编码。在 FormTableViewController 中，您需要覆盖两个函数，即 TableView 的 didSelectRowAt 和 heightForRowAt。

```
import UIKit

class FormTableViewController: UITableViewController {

    private var dateCellExpanded: Bool = false

    override func viewDidLoad() {
        super.viewDidLoad()
        // For removing the extra empty spaces of TableView below
        tableView.tableFooterView = UIView()
    }

    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        if indexPath.row == 0 {
            if dateCellExpanded {
                dateCellExpanded = false
            } else {
                dateCellExpanded = true
            }
            tableView.beginUpdates()
            tableView.endUpdates()
        }
    }

    override func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        if indexPath.row == 0 {
            if dateCellExpanded {
                return 250
            } else {
                return 50
            }
        }
        return 50
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将很快解释代码。变量 dateCellExpanded 是一个标志，用于确定所选的当前单元格是否已经展开。然后，在总是调用 *tableView.beginUpdates()* 和 *tableView.endUpdates()* 之后，就会触发 TableView heightForRowAt 的委托函数来决定我们要不要返回什么高度值。是的，就是这么简单。点击运行！

[![1-_TMKiaY6yEn4iGw2w_vdvQ](img/837e65316eb08225f79c5025657a56c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zCdzG6U_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://lawgimenez.me/conteimg/2018/07/1-_TMKiaY6yEn4iGw2w_vdvQ.gif)

希望这个演示能让你知道如何在你的应用上实现它。

范例项目可在[https://github.com/lawgimenez/expandable-cell-test](https://github.com/lawgimenez/expandable-cell-test)找到