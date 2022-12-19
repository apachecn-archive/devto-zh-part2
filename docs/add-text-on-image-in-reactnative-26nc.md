# 在反应图像上添加文本

> 原文：<https://dev.to/chansuke/add-text-on-image-in-reactnative-26nc>

如果你想在图片上放一个简单的文本(比如状态或类别)，使用`ImageBackground`非常有用。

```
<ImageBackground
  source={{ uri: hoge }}
  style={{
    height: 100,
    width: 100,
    position: 'relative', // because it's parent
    top: 2,
    left: 2
  }}
>
  <Text
    style={{
      fontWeight: 'bold',
      color: 'white',
      position: 'absolute', // child
      bottom: 0, // position where you want
      left: 0
    }}
  >
    Hello World
  </Text>
</ImageBackground> 
```

Enter fullscreen mode Exit fullscreen mode

将组件作为`<ImageBackground>`的子组件，并根据需要调整位置。