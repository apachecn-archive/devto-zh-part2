# 如何在 Roku(BrightScript/SceneGraph)上的组件接口中定义函数

> 原文：<https://dev.to/umitic/how-to-define-a-function-in-component-interface-on-rokubrightscriptscenegraph-dff>

如果您有一个名为“doWhateverScreen”的自定义组件，并且您想使用“doWhateverScreen”组件接口中的函数打印一个从另一个名为“HomeScene”的组件传来的字符串，那么您应该这样做。

首先在您的 doWhateverScreen.xml 文件中添加:

```
 <interface>
        <function name="doWhateverMan" />
    </interface> 
```

Enter fullscreen mode Exit fullscreen mode

现在在 doWhateverScreen.brs 文件中添加您的“doWhateverMan”函数:

```
 Function doWhateverMan(param as String)
        print param
    End Function 
```

Enter fullscreen mode Exit fullscreen mode

很好，到目前为止一切顺利！让我们继续。在 HomeScene.xml 中添加这个自定义创建的“doWhateverScreen”屏幕/组件，并在 HomeScene.brs init()函数中添加:

```
 m.doWhateverScreen= m.top.findNode("doWhateverScreen") 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以从 HomeScene.brs 调用一个名为“doWhateverMan”的函数:

```
 param = "Do Androids Dream of Electric Sheep?"
    m.doWhateverScreen.callFunc("doWhateverMan",param) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！祝你愉快。:-D