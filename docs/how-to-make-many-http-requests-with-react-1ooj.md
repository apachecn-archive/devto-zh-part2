# 如何用 react 发出许多 http 请求

> 原文：<https://dev.to/ekimkael/how-to-make-many-http-requests-with-react-1ooj>

嗨，拜托，我卡住了。有人知道如何在 react 组件或路径中发出许多请求吗？

```
componentDidMount = () => {
    const { match: { params } } = this.props
    fetch(`/w/${params.work}`)
      .then(res => res.json())
      .then(work => this.setState({ work }))

    fetch('/works')
      .then(res => res.json())
      .then(otherWorks => this.setState({ otherWorks }))
} 
```

Enter fullscreen mode Exit fullscreen mode

像这样有用的东西。
我是一个用**反应**的初学者，所以我真的不想要像**还原**T5 这样的东西，我只是想要一个初学者的方式。第一次提取有效，但第二次不有效