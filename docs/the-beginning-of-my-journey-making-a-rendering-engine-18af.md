# 我旅程的开始:制作渲染引擎

> 原文：<https://dev.to/superfola/the-beginning-of-my-journey-making-a-rendering-engine-18af>

*Nota Bene* :这一系列的文章主要集中于我在制作 *Voksel* 这款《我的世界》风格的 3D 游戏时的经历，所以我将只写我的看法以及我是如何解决我遇到的问题的！

一个月前，在自学了 OpenGL 之后，我有了这个奇怪的想法:如果我自己做一个渲染引擎会怎么样？这可能很有趣，我可以学到很多东西！我就是这样开始制作 [Zavtrak](https://gitlab.com/SuperFola/Zavtrak) 的。几天后，我想到了另一个主意:如果我用这个引擎制作我自己版本的《我的世界》会怎么样？我的旅程就这样开始了，在九月一个阳光明媚的日子里！

我做了一件小事来知道我要去哪里:

*   抽象代码用于:
    *   VAO、VBO、EBO
    *   着色器
    *   自由飞行照相机
    *   窗口创建

(我的目标过去是，现在仍然是，为 OpenGL 创建一个几乎零成本的 3D 渲染库)

VAO 充当存储顶点(每个 3D 点的数据集合)属性指针的数组，当给定 VBO(顶点缓冲对象，存储顶点)时，OpenGL 使用该数组来了解它是否在查看位置、颜色、纹理坐标...在给定的顶点。

EBO(元素缓冲对象)是一个数组，存储顶点的索引(存储在给定的 VBO 中)以“链接”它们:

```
vertices = [
    # position      # color
    [0.0, 0.0, 0.0, 1.0, 0.5, 0.0],
    [0.0, 1.0, 0.0, 0.0, 0.5, 1.0],
    [1.0, 0.0, 0.0, 0.5, 1.0, 0.5]
]
elements = [
    0, 1, 2
] 
```

Enter fullscreen mode Exit fullscreen mode

定义一个直角三角形(将数据交给 OpenGL 时)。EBO 通过仅定义要使用的元素的索引，避免了多次重复相同的顶点，这在给 GPU 提供大量顶点时会非常繁重。

着色器是一个小程序(用 [GLSL](https://en.wikipedia.org/wiki/OpenGL_Shading_Language) 编写),告诉 OpenGL 如何绘制给定顶点的单个像素。

我的想法是这样的:

```
auto shader = zk::Shader("shader.vert", "shader.frag");

zk::Vertices vertices({
    // positions         // colors
     0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,   // bottom right
    -0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,   // bottom left
     0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f    // top 
});
zk::objects::VertexArray VAO;
zk::objects::VertexBuffer VBO;
// bind the Vertex Array Object first, then bind and set vertex buffer(s)
// and then configure vertex attributes(s).
VAO.bind();
VBO.setData(vertices);
zk::objects::setVertexAttrib(0, 3, 6, 0);
zk::objects::setVertexAttrib(1, 3, 6, 3);

shader.use();
// do drawing... 
```

Enter fullscreen mode Exit fullscreen mode

这基本上和[这个](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/2.1.hello_triangle/hello_triangle.cpp)是一样的(没有窗口配置和所有的，你可以在这里找到)，更短更容易理解，嗯？

我现在遇到的最大问题是我们必须自己配置一切(设置顶点属性，创建 VAO 和 VBO，绑定它们...)(而且我还是没有修好，因为我想专注于游戏，以后重写引擎)。

写了很多[冗余代码](https://gitlab.com/SuperFola/Zavtrak/blob/master/include/Zavtrak/common/events.hpp)之后，我可以开始用它写一个小游戏了(你可以在这里找到我的实验[)。我刚开始写游戏时最大的纠结就是顶点属性(你可以在封面图片上看到，我把顶点属性搞砸了，OpenGL 处理得好像一切正常)。](https://gitlab.com/SuperFola/Zavtrak/tree/master/examples)

[![GIF: everything is fine](../Images/2d3b632c5ed3fc3ad181644c1a60d32f.png)T2】](https://i.giphy.com/media/z9AUvhAEiXOqA/giphy.gif)

这是本系列的第一篇文章，与其说是“编写渲染引擎的问题”，不如说是一篇“解释情节”的文章，但是如果你想知道我自己制作渲染引擎的利弊，这里有:

赞成的意见

*   你控制一切
*   你可以给它你想要的接口(我的意思是，不是图形化的，而是程序化的)
*   你知道引擎盖下是怎么工作的

骗局

*   编写自己的渲染引擎真的很难(我做了一个非常简单的引擎，因为 Zavtrak 是 OpenGL 的抽象层)
*   这可能要花很多时间(还有头疼！)

* * *

我现在将简要解释我制作这个视频游戏的目的。

正如我所说，这是一款《我的世界》风格的 3D 探索游戏，玩家可以探索一个由 3D 像素组成的程序生成的世界。这个项目背后的目标是创造一个与 [Proteus](http://twistedtreegames.com/proteus/) 精神相同的游戏:一个视听探索游戏，但有更多的东西:帮助玩家放松。

* * *

附:我一口气写了这篇文章，只记住了“我该如何向你介绍我们在创建游戏时遇到的问题”，所以它可能会很脱节，对此我很抱歉