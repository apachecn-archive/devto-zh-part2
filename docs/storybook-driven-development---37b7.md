# 故事书驱动开发を試している

> 原文：<https://dev.to/jgs/storybook-driven-development---37b7>

因为尝试了很久以前就很在意的 Storybook，在产品中也试着采用了，所以写下感想。

顺便说一下，虽然使用了[Next.js](https://github.com/zeit/next.js) ，但是如果进行了`.babelrc`的设定之类的话，在`start-storybook`的`-s ./`选项中就没有问题了。

# Motivation

这几年，我做过几次用 React 以 component 为基础设计网页的事情，但还是完成了意大利面的组件。 而且意大利面组件群往往难以开发...

# 关于 Storybook

[![](../Images/3311a5fd6a410b2d91807dd61f0685c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ggmZ-sGU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/4e5bee38519cf776e97452d380d7b305.png)

Storybook 是一种可以动态生成样式指南的工具，不仅支持 React，还支持 Vue 和 Angular。

安装`@storybook/react`后从 CLI 启动即可热加载的画面启动，非常方便。

https://storybook.js.org/examples/サンプルはでみれる。

# 关于原子设计

[![](../Images/69a194ed3383453b04a970d7b2c7c486.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--woA-tEty--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/1e42493f2e45b243085bf9571b593212.png)

(这就是全部吧)

[![](../Images/790508f0b5b84e6abef0767aa49c5c49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MeTT6R7Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/758f97c15a3e4721f5a4c020125d04e7.jpg)

(有名的图)

我认为写 Storybook 的时候，在某种程度上进行项目划分比较好吧。 于是想起了 Atomic Design。 以前知道的时候觉得分类好像很麻烦，但决定暂时遵从一下。

# 开发流程

首先从小品开始作为 Atom 写在 Storybook 上。 根据状态而变化的组件列举状态，以便可以在 Storybook 上进行确认。 首先 Atom 和 Molecules 们完成了。

各部件完成后需要与 API 连接的 Organisms 等，采取行动。 这里有用的是 recompose。

# 关于 recompose

[recompose](https://github.com/acdlite/recompose) 是面向 Lodash 类 React (特别是 HoC )的实用带。

```
const enhance = withState('counter', 'setCounter', 0)
const Counter = enhance(({ counter, setCounter }) =>
  <div>
    Count: {counter}
    <button onClick={() => setCounter(n => n + 1)}>Increment</button>
    <button onClick={() => setCounter(n => n - 1)}>Decrement</button>
  </div> ) 
```

Enter fullscreen mode Exit fullscreen mode

为了只提取以这种方式管理外观的组件，准备了各种各样的函数。

例如，如果是 React 的生命周期函数，则为

```
const PostsList = ({ posts }) => (
  <ul>{posts.map(p => <li>{p.title}</li>)}</ul>
)

const PostsListWithData = lifecycle({
  componentDidMount() {
    fetchPosts().then(posts => {
      this.setState({ posts });
    })
  }
})(PostsList); 
```

Enter fullscreen mode Exit fullscreen mode

可以这样分离。

另外，它们也可以通过`compose`函数将它们组合起来。

```
const enhance = compose(
  withState('counter', 'setCounter', 0),
  lifecycle({...})
)

enhance(Counter) 
```

Enter fullscreen mode Exit fullscreen mode

将来要是能用 enzyme 在 Storybook 上确认动作就好了，但一旦在 Storybook 上贯彻了风格指南

```
export const Counter = ({...}) => (...)

const enhance = compose(...)

export default enhance(Counter) 
```

Enter fullscreen mode Exit fullscreen mode

作为，在 story 中读取`import {Counter} from '...'`，从 Pages 中读取`import Counter from '...'`，可以分为外观上的组件和动作的组件。

从外观上看，它在 Storybook 上做得很完美，所以写下动作的逻辑就能很好地发挥作用，所以很舒服。

# 今后的展望等

我想试试 Storybook 文档中也写的 visual regression test。 接下来，使用上面也写了一点的 enzyme 进行动作测试等。

# 总结

尝试导入使用 Storybook 对遵循 Atomic Design 的组件群进行外观修整后，再使用 recompose 进行动作的开发流程。

闲话不多说，粗略看了一下 Examples，Wix 的 wix-style-react 被定制得非常好用，真的很厉害... 希望能正式纳入的水平。