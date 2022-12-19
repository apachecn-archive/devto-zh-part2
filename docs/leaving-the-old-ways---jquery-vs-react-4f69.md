# 离开旧的方式——jQuery vs React

> 原文：<https://dev.to/jsguru_io/leaving-the-old-ways---jquery-vs-react-4f69>

[![Main Image, React logo on thumbs up](../Images/a9f0b44215c6bfb720259dd2b8e133c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bZogLPCI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://delivery.eusi.cloud/api/v1/f1a4305c-e431-4668-ae4c-02f78c656a41/media/s3/1534422989797_react-vs-jquery-header.png) 
与普遍的看法相反，React 最大的用例不是 spa，而是最常见的混合应用，并且在我看来是最适合的，在这篇文章中，我将讲述我是如何以及为什么从一个 React 讨厌者变成 React 粉丝，以及为什么 React 是 jQuery 的完美替代品。

* * *

当 React 和 Vue 第一次开始获得牵引力，并成为构建现代 ui 的事实标准时，我曾经有过某种内心的抗拒。

是的，我故意把 Angular 排除在外，尽管 AngularJS 是给我们带来 web 2.0 的前端革命的先驱。

Angular 在哲学上完全相反，它是一个成熟的 SPA 框架，而 React 只是一个视图库，我仍然不相信 SPA 是正确的方式，我个人更喜欢混合方式。

对于所有正在思考的人来说——“那么 Vue 呢？”，Vue 会介于这两个极端之间。

与普遍的看法相反，React 最大的用例不是水疗，而是最常见的混合应用，在我看来也是最适合的。不相信我？看看脸书的丹·阿布拉莫夫是怎么说的:

> 有趣的是，React 与 spa 联系如此紧密，但脸书并没有将它用于 spa(除了 Instagram 网站和一些内部工具)- *[@dan_abramov](https://dev.to/dan_abramov)*

我最讨厌的是 Webpack 和他们自带的所有工具。

我强烈认为他们在前端引入了不必要的复杂性，是的，他们让我们开发人员感觉像火箭科学家一样，我们必须做大量的调整，我们必须拉动和转动大量的杠杆和齿轮才能使它们运行，但最终，他们真的为业务增加了价值吗？

当我们可以用普通的 ole jQuery 或者更好的 vanilla JS 做同样的事情时，他们是否改进了产品和用户体验，以保证更高的维护和开发成本以及新成员更高的准入门槛？

当我发现 React 引入了 react-cli 后，我决定再试一次，天啊，天啊，我又惊又喜。

随着 react-cli(和 vue-cli)的引入，80 %- 90%的用例不再需要所有的基础工具和那些相当于获得计算机科学博士学位的构建步骤，尽管对于一些边缘用例，您仍然需要卷起袖子使用 webpack。

当然，如果你正在构建一个相当简单的东西，可能是一个带有 Ajax submit 的联系表单或者其他完全不同的东西，但这已经足够简单了，在我看来，香草 JS 是一个很好的方法，没有必要推出大枪。你甚至可以用 jQuery，但在当今世界真的不需要它，但那是完全不同的话题。

> 保持简单- *这应该永远是你的口头禅*。

在这种情况下，如果你要使用一个框架，90%的代码是框架的基础设施，剩下的就是你的实际逻辑。这是一种大材小用，引入了不必要的样板文件，增加了包的大小，直接影响了性能。更大的捆绑包意味着更多的字节必须通过互联网发送，所以你实际上是在为你的业务付出代价，只是因为你想使用这个闪亮的新东西。

哦，你以为那几毫秒没多大关系？嗯，它们可以很快增加，特别是在高流量的网站上，仅仅因为今天的机器功能强大并不意味着我们应该不计后果地向它们扔东西，我们需要保守地对待我们的资源。

这样来看，就好像你在为一座十层楼的建筑打地基，却在上面搭了一个帐篷。

与旧的方式相比，React 在构建复杂的 ui 时会大放异彩。

使用 React，开发的简单性随着您正在构建的 UI 的复杂性而增加，或者换句话说，与普通的 JS/jQuery 方法相比，开发的成本与复杂性成反比。

这里有一个小图表，适用于所有视觉类型。
[![Graph representing how the simplicity of implementation increases with the complexity of your UI when using React](../Images/41cd8191da704fae9584dddfbdc71785.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--V3kYN-97--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://delivery.eusi.cloud/api/v1/f1a4305c-e431-4668-ae4c-02f78c656a41/media/s3/1534422990085_react-vs-jquery-graph.png)

空谈是廉价的，让我们用一个真实世界的例子来弄脏我们的手。

除了发票日期、发票到期日、主题等一般数据之外，我们还有发票表单。，用户需要能够添加/删除发票项目。

另一方面，发票项目具有以下内容:

*   您开具发票的产品/服务的名称和/或描述，
*   这是数量，
*   价格，
*   你可能给的任何折扣，
*   产生的任何罚息，
*   根据你们国家的法律，我们可能会征收增值税或销售税

最后，前面提到的所有计算。
[![Image representing a complex form](../Images/0b2c5eb4c9c9130d0009cde11415b1a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U5vRNBdo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://delivery.eusi.cloud/api/v1/f1a4305c-e431-4668-ae4c-02f78c656a41/media/s3/1534422989041_react-vs-jquery-form.png) 
你现在看到一件看似简单的事情怎么会变得复杂得很快了吧？

使用旧方法，您必须考虑很多事情，您需要:

1.  在所有不同的输入字段上添加变更事件处理程序，其中一些还需要相互抵消，因此您需要跟踪何时分离它们。

2.  每次添加或删除发票项时，您都需要操作 DOM，要么添加或删除子节点，要么将 HTML 写成字符串。

    无论如何选择，你都需要连接一些 HTML 并用变量填充它，这很快就会变得难以控制。ECMA 6 字符串文字确实缓解了这一点，但仍然会变得很麻烦。

    想象一下，一个设计师改变了一些东西，你需要在多少地方改变你在普通 JS 代码中粘合在一起的所有那些位？

    你需要记住的另一件事是，如果你把 DOM 作为一个字符串来操作，你就杀死了那些特定 DOM 元素上的所有事件处理程序。是的，又一个抓住你的时刻。

3.  计算——每次添加或删除发票项目时，您都需要计算其特定值，此外还要更新发票的小计、税、总额等。本质上，你可以创建自己的州商店。

我可能已经错过了在尝试用老方法处理这个用例时会出现的一两件事情，因为它通常是，在你开始实现它之前，一切在纸上听起来都很简单，并且需要处理的全新的案例出现了。

使用 React 需要稍微改变一下心态，简而言之，你只需要关心一件事，状态。这极大地简化了逻辑，您只关心您的状态，这是您唯一需要操作的东西，并且您的发票输入字段和发票项目将根据您状态的变化重新呈现。

让我们来看看我们的简化代码示例，这可能会给你一个更清晰的画面。

```
import React from "react";
import ReactDOM from "react-dom";
import PropTypes from "prop-types";

class InvoiceItemForm extends React.Component {

  constructor(props) {
      super(props)

      this.state = {
          itemInput: {
              description: '',
              quantity: 0,
              price: 0,
              subtotal: 0,
              taxRate: 0.17,
              tax: 0,
              total: 0
          },
          invoiceItems: []
      }

      this.handleInputChange = this.handleInputChange.bind(this);
      this.addItem = this.addItem.bind(this);
      this.removeItem = this.removeItem.bind(this);
  }

  handleInputChange(e) {
      let input = (e.target || e.currentTarget);
          input.subtotal = input.price * input.quantity;
          input.tax = input.subtotal * input.taxRate;
          input.total = input.subtotal * (1 + input.taxRate);

      this.setState((state) => { return state.itemInput[input.name] = input.value; });
  }

  addItem() {
      let state = this.state;
          state.items.push(state.itemInput);

          // Clear the last input
          for (let key in state.itemInput) {
              switch (key) {
                  case 'description'
                      state.itemInput[key] = '';
                      break;
                  case 'taxRate':
                      state.itemInput[key] = 0.17;
                      break;
                  default:
                      state.itemInput[key] = 0;
                      break;
              }
          }

      this.setState({itemInput: state.itemInput, items: state.items});
  }

  removeItem(e) {
      let rowIndex = (e.target || e.currentTarget).parentNode.parentNode.rowIndex;

      let items = this.state.items.filter((item, i) => { return i !== rowIndex; });

      this.setState({items : items});
  }

  renderCells(item, rowIndex) {
      let cells = [<td>{rowIndex + 1}</td>];

      let i = 1;
      for (let key in item) {
          cells.push(<td key={i}>{item[key]}</td>);
          i++;
      }

      cells.push(
          <td>
              <button onClick={this.removeItem}>
                  {'Remove Item'}
              </button>
          </td>
      );

      return cells;
  }

  render () {
    return (
        <React.Fragment>
            <div>
                <input
                    name={'description'}
                    value={this.state.itemInput.description}
                    onChange={this.handleInputChange} />
                <input
                    name={'price'}
                    value={this.state.itemInput.price}
                    onChange={this.handleInputChange}>
                <input
                    name={'quantity'}
                    value={this.state.itemInput.quantity}
                    onChange={this.handleInputChange}>
                <input
                    name={'taxRate'}
                    value={this.state.itemInput.taxRate}
                    onChange={this.handleInputChange}>
                <input
                    name={'subtotal'}
                    disabled={true}
                    value={this.state.itemInput.subtotal}
                    onChange={this.handleInputChange}>
                <input
                    name={'tax'}
                    disabled={true}
                    value={this.state.itemInput.tax}
                    onChange={this.handleInputChange}>
                <input
                    name={'total'}
                    disabled={true}
                    value={this.state.itemInput.total}
                    onChange={this.handleInputChange}>
            </div>
            <table>
                <thead>
                    <tr>
                        <th>Item no.</th>
                        <th>Description</th>
                        <th>Price</th>
                        <th>Quantity</th>
                        <th>Tax Rate</th>
                        <th>Subtotal</th>
                        <th>Tax</th>
                        <th>Total</th>
                        <th></th>
                    </tr>
                </thead>
                <tbody>
                    {
                        this.state.items.map((item, i) => {
                            return (
                                <tr key={i}>
                                    {this.renderCells(item, i)}
                                </tr>
                            );
                        })
                    }
                </tbody>
            </table>
        </React.Fragment>
    );
  }
}
export default InvoiceItemForm 
```

Vuoala，就是这样！

嘿，你有想要移植的 jQuery 应用程序吗，或者你只是想找出哪个框架最适合你的下一个百万美元想法？请通过 [info@jsguru.io](mailto:info@jsguru.io) 联系我们，让这成为我们的头痛问题。

* * *

#### 出发前…

如果你喜欢读这篇文章，请分享它。你应该看看我们的其他出版物，你可能也会喜欢它们！我们不时地写一些关于软件开发、技巧和诀窍，以及如何成为一个更好的开发人员和商业人士的文章。加入我们不断改进的旅程吧！

在[脸书](https://www.facebook.com/jsguruio/)、[推特](https://twitter.com/jsguru_software)、 [LinkedIn](https://www.linkedin.com/company/jsguru) 、 [Medium](https://medium.com/jsguru) 或 [DEV.to](https://dev.to/jsguru_io) 上关注我们。

* * *

最初发布于 [jsguru.io](https://jsguru.io) 。