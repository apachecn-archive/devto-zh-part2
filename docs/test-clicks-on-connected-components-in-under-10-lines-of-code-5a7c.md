# 在不到 10 行代码中测试点击连接的组件

> 原文：<https://dev.to/gsto/test-clicks-on-connected-components-in-under-10-lines-of-code-5a7c>

这里有一个相当良性的组件，它可以让开发人员在为你的组件编写单元测试的时候暂停一下:

```
const ClickableButton = props => (
  <button onClick={props.doSomething}>Click Me!</button> )
const mapDispatchToProps () => ({
  doSomething: dispatch(someFancyAction())
})
export default connect(
  null, 
  mapDispatchToProps,
)(ClickableButton) 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一值得测试的是:当我们单击按钮时，我们作为道具传入的函数被调用。这个适当功能可能是一个复杂的动作链& API 调用。你不会想强调这一点，至少在这些测试中不会。但是要做到这一点，我们是否必须创建一个模拟商店和一个提供者组件，仅仅因为它是连接的？啊！

一定有更好的方法。

## 您可以完全删除冗余连接

虽然您希望您的组件是默认的导出，但是您可以导出未连接的组件，并且只需测试它！感谢 Dave Ceddia 向我展示了一种更好的方法来[导出一个连接的组件](https://daveceddia.com/redux-connect-export/)。你只需要改一行:

```
export const ClickableButton = props => ( 
```

Enter fullscreen mode Exit fullscreen mode

作为一个额外的技巧，如果您有任何没有附加到组件的帮助函数，您也可以导出它们以便于测试。

## 有了笑话和酶，剩下的就容易了

在我们的测试中，导入未连接的组件。然后，您可以使用 Jest 创建一个[模拟函数，而](https://jestjs.io/docs/en/mock-functions)[使用 Enzyme](https://airbnb.io/enzyme/docs/api/ReactWrapper/simulate.html) 模拟点击。下面是这个测试的总体情况:

```
describe('<ClickableButton />', () => {
  it('calls the doThing prop when the button is clicked', () => {
    const props = { doSomething: jest.fn() }
    const wrapper = shallow(<ClickableButton {...props} />)
    wrapper.find("button").first().simulate("click")
    expect(props.doSomething).toHaveBeenCalled()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

post [测试点击连接组件不到 10 行代码](https://glennstovall.com/test-connected-component-clicks/)最早出现在[格伦·斯托瓦尔](https://glennstovall.com)上。