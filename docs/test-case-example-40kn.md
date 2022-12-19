# 测试案例示例

> 原文：<https://dev.to/egitsptr/test-case-example-40kn>

### 这是 bug 报告吗？

> 是

### 你看过 Contributing to React 原生指南的 bug 部分吗？

> 是

### 环境

1.  `react-native -v` : 0.4.5
2.  `node -v` : 7.4.0
3.  `npm -v` : 5.0.3
4.  `yarn --version`(如果使用纱线):0.24.5

然后，指定:

1.  目标平台(如 iOS、Android): iOS
2.  开发操作系统(如 macOS Sierra、Windows 10): macOs Sierra
3.  构建工具(Xcode 或 Android Studio 版本，iOS 或 Android SDK 版本，如果相关):XCode 8.3.3

### 步骤重现

1.  在模块中实现 React-Native FlatList。
2.  在 renderitem prop 中渲染组件
3.  将平面列表嵌套在``元素中
4.  拉起设备以尝试刷新

### 预期行为

> 上拉时调用 onRefresh 函数。

### 实际行为

> 未调用 onRefresh 函数。

### 可重复演示

> 小吃链接:[https://snack.expo.io/HJYx3Kx4W](https://snack.expo.io/HJYx3Kx4W)

```
...
  constructor(props) {
    super(props);
    this.state = {
      stories: [],
      isFetching: false,
    };
  }
  componentDidMount() { this.fetchData() }
  onRefresh() {
    this.setState({ isFetching: true }, function() { this.fetchData() });
  }
  fetchData() {
    var that = this;
    axios.get('http://192.168.0.13:3000/api/story/get/by/geo')
      .then((res) => {
        that.setState({ stories: res.data, isFetching: false });
        that.props.dispatch(StoryActions.setStories(res.data))
      })
  }
  render() {
    return (

         this.onRefresh()}
          refreshing={this.state.isFetching}
          data={this.state.stories}
          keyExtractor={(item, index) => item.id}
          renderItem={({item}) => ( )}
          />

    )
  }

```

*起源链接:[https://github.com/facebook/react-native/issues/14756](https://github.com/facebook/react-native/issues/14756)T3】*