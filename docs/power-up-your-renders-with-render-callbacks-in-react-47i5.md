# 使用 React 中的渲染回调来启动渲染

> 原文：<https://dev.to/moimikey/power-up-your-renders-with-render-callbacks-in-react-47i5>

您还不能完全感受到它的强大，但是我们将通过像孩子一样传递一个函数来增强我们的渲染技能:

```
<React.Fragment>
    {() => {}}
</React.Fragment> 
```

Blep。

[![OvbGwwI.jpg](../Images/e8f1df3655d46533803e7651ad8a51f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AiU9QBfb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/yR3OeJATbKZx109BUvnm)

嗯，那不是很令人兴奋。至少现在还没有。我们首先需要的是一个想法。让我们建立一个共享工具栏！这很简单。我们将有一排图标:一个与脸书共享；一个分享到 Twitter 一个通过电子邮件发送；一个用来复制永久链接。听起来很简单。我在很多网站上都看到过这些。

```
function ShareToolbar(props) {
    return (
        <div>
            <a href="javascript:">Facebook</a>
            <a href="javascript:">Twitter</a>
            <a href="javascript:">Email</a>
            <a href="javascript:">Permalink</a>
        </div>
    );
} 
```

现在看起来有点像了。没什么特别的。但是，我想在这里多做一点。我希望能够设计每个链接。可能把它们变成带有 SVG 图标的社交图标按钮。我可能希望他们有计算过的超链接。我还想为 permalink 创建一个“抽屉”，这样我们就可以在可爱的按钮旁边看到一个简短的 url。

[![giphy-downsized.gif](../Images/3a50517a6f563fb44ef4a5d092086d77.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IpLJy9vn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.filestackcontent.com/N88nkuDfRJKEl9yo0zok)

```
const Item = (props) =>
    <Item onClick={props.onClick} href={props.href}>{props.children}</Item>;

const Button = (props) =>
    <Button>{props.children}</Button>;

const Drawer = (props) =>
    props.isOpen ? <Drawer>{props.children}</Drawer> : null;

function ShareToolbar(props) {
    return (
        <React.Fragment>
            {props.children ? props.children({
                    Item,
                    Button,
                    Drawer
            }) : null}
        </React.Fragment>
    );
}

export default ShareToolbar 
```

事情变得很复杂。但是，至少是可读的！(希望如此)。然而，我们正在做的正是第一个例子中所做的，而不是返回一个空对象，或者在第二个例子中，我们返回 JSX，我们调用子道具作为函数(如果它存在的话)

为什么这句话几乎没有意义？

**调用子道具作为函数**

这可能是因为一开始很难想象，直到我们从一个例子中发展出来。让我们反其道而行之，设计我们*希望*如何使用组件:

```
<ShareToolbar>
    {({ Item, Button, Drawer }) => {
        return (
            <Toolbar>
                <Item href='https://facebook.com'>
                    <Button>
                        <Icon name='facebook' />
                    </Button>
                </Item>
                <Item href='https://twitter.com'>
                    <Button>
                        <Icon name='twitter' />
                    </Button>
                </Item>
                <Item href='https://gmail.com'>
                    <Button>
                        <Icon name='mail' />
                    </Button>
                </Item>
                <Item onClick={() => {
                    copy('https://google.com');
                    alert('copied to clipboard.');
                }}>
                    <Button>
                        <Icon name='link' />
                    </Button>
                    <Drawer isOpen>
                        https://google.com
                    </Drawer>
                </Item>
            </Toolbar>
        );
    }}
</ShareToolbar> 
```

这读起来更清楚(我希望)。我们看到的是伪代码，然而，借助样式化组件的力量，命名约定实际上可以保持不变。他们毕竟只是 div。

```
{({ Item, Button, Drawer }) => { // the render callback, `ShareToolbar.children`
    ...
}} 
```

还记得我们如何调用函数`props.children`吗？这就是那个被调用的函数。它被挤压在`ShareToolbar`组件之间。

这种模式的强大之处在于，它允许我们抽象组件的视觉层次，并提供动态组合视图的能力，我们希望如何呈现它，而不是以一种特定的方式呈现。

您会注意到，当从数据层中抽象出视图层时，这种模式开始变得更有意义，在数据层中，数据是作为呈现回调中的值传递的，而不是组件。你可以在我的下一篇文章中读到更多，使用渲染回调来连接非连接组件。

本文首发于 code mentor[https://www . code mentor . io/moi Mikey/power-up-your-renders-with-render-callbacks-MB 96 th j33](https://www.codementor.io/moimikey/power-up-your-renders-with-render-callbacks-mb96thj33)