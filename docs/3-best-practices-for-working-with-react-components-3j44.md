# 使用 React 组件的 3 个最佳实践

> 原文：<https://dev.to/educationecosystem/3-best-practices-for-working-with-react-components-3j44>

(本文原载于我们的博客[此处](https://blog.liveedu.tv/3-best-practices-for-working-with-react-components/))。

[React.js](https://reactjs.org/) (也称为 ReactJS 或 React)是一个流行的 JavaScript 库，用于创建精彩的用户界面。

React 的一个显著特点是它依赖于一种以组件为中心的方法来构建交互式 ui。

Maxim-Filimonov 在软件开发行业有十多年的经验，目前正在向人们传授他的技能，他说“在创建组件时遵守 React API 最佳实践将有助于您编写高质量和干净的 React 代码。”

[点击此处](https://www.liveedu.tv/maxim-filimonov/2b1rr-how-to-build-cross-platform-mobile-app-in-react-native/gYoKx-intro-video1/)观看并学习他的一个项目，了解如何在 React Native 中构建跨平台的移动应用。

这里有三个 React 技巧和最佳实践，可以让你的开发技能更上一层楼。

## 1。避免创建不必要的新组件

下面是一个 React 组件的示例:

```
export default class Liveedu extends PureComponent {
  static propTypes = {
    userIsLearning: PropTypes.bool,
    user: PropTypes.shape({
      _id: PropTypes.string,
    }).isNeeded,
  }

  static defaultProps = {
    userIsLearning: false,
  }

  render() {
    const { userIsLearning, user } = this.props;
    if (!userIsLearning) return <Learning />;
    return (
      <div>
        <div className="two-col">
          <section>
            <LearnOnline userId={user.id} />
            <Watching projects userId={user._id} />
          </section>
          <aside>
            <UserDetails user={user} />
            <WatchProjects user={user} />
          </aside>
        </div>
        <div className="one-col">
          {isInfo('student', user={user._id} &&
            <LearnersInfo userId={user._id} />
          }
        </div>
      </div>
    )
  }
} 
```

从上面的代码可以看出，我们有一个名为 *Liveedu* 的组件。我们在这个庞大的组件中加入了其他组件，如 *LearnOnline* 和 *WatchProjects* 。

由于我们从同一个位置(*用户*)获取所有数据，我们可以单独声明组件。然而，为了达到简洁，我们选择将较小的组件包含在一个组件中。

尽管没有关于如何为 React 代码创建新组件的黑白规则，但以下准则是有价值的:

*   如果您打算重用您的代码，请考虑为每个功能创建新组件。
*   如果您希望每个组件代表一个特定的功能，那么制作新的组件可能是一个好主意。
*   如果你的代码变得笨拙和混乱，通过创建新的组件来提高可读性。

## 2。知道何时使用组件、无状态功能组件和纯组件

创建 React 代码的另一个最佳实践是知道何时使用各种类型的组件。

下面是一个代码示例，展示了如何创建无状态功能组件:

```
const Liveedu = () => (
  <WatchProjects>
    <H1>Programming Projects</H1>
    <div className="learn_online">
      <Link className="liveedu_project_image" to="/">
        <img alt="experts building projects" src="liveedu.jpg">
      </Link>
      <div className="Project Learning Platform">
        <h2 className="create">Programming Projects</h2>
        <li>JavaScript</li>
        <li>PHP</li>
        <li>Angular</li>
      </div>
    </div>
  </WatchProjects> ); 
```

这种类型的组件将帮助您编写干净整洁的 React 代码。

你可以使用它们来制造不依赖于任何类型的*引用*、*状态*或其他*生命周期方法*的组件。

因此，您不必担心状态操作或生命周期方法，它们不会强迫您安装用于执行测试的库。

顾名思义，这个组件是没有任何状态的；它只是一个函数。因此，它帮助您定义一个组件，就像返回所需数据的常量函数一样。

换句话说，这是一个用于返回 JSX 的函数。

在上面的第一个代码示例中，您可能已经意识到我们将 *Liveedu* 声明为 *PureComponent* ，而不是使用传统的*组件*。

PureComponent 通常用于防止 React 进行不必要的重新渲染。每当一个组件收到一个新的道具，它会自动重新渲染。

即使组件收到了没有任何更改的新道具，也会发生这种情况。

例如，如果一个属性引用了一个字符串或布尔值，并且发生了变化，那么一个 *PureComponent* 可以检测到这一点。

另一方面，每当对象内部的属性经历任何变化时， *PureComponent* 不能启动 React 重新渲染。

因此，您可以使用 *PureComponent* 来代替*组件*，以确保仅在必要时进行重新渲染。

## 3。谨慎使用扩散属性

*…* (三点)spread 操作符对于实现 React 代码的简洁非常有用。

下面是一个代码示例，它选择组件使用的特定道具，并使用 spread 运算符传递整个道具对象

```
const Liveedu = props => {
  const { kind, ...other } = props;
  const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
  return <button className={className} {...other} />; };

const App = () => {
  return (
    <div>
      <Button kind="primary" onClick={() => console.log("clicked to start learning")}>
        Watch Experts Create Practical Projects
      </Button>
    </div>
  );
}; 
```

在上面的代码中，*种类的*道具被消耗掉了，没有任何问题。此外，它不会传递给文档对象模型(DOM)中的 *<按钮>* 元素。

此外，使用 *…other* 对象传递其他属性，这为该组件增加了一些灵活性。事实上，它传递了一个 *onClick* 和 *children* 道具，如代码所示。

尽管在组件中使用 spread 属性可能是有益的，但它增加了将不必要的属性传递给不关心其存在的组件的机会。

此外，这种语法会让您将不必要的 HTML 属性传递给 DOM。

因此，谨慎使用它们更好。

## 包装完毕

React 组件是创建强大而直观的用户界面的基石。

上面提到的最佳实践会给你信心，你需要[抓住 React.js 的角](https://www.liveedu.tv/guides/programming/react-js/)，把你的前端开发技能提升到下一个层次。

你还知道哪些 React.js 最佳实践？

请在下面分享你的评论。