# 让我们用 OpenGL 渲染体素块！

> 原文：<https://dev.to/superfola/lets-render-chunks-of-voxels-with-opengl--47g9>

在我的[上一篇](https://dev.to/superfola/the-beginning-of-my-journey-making-a-rendering-engine-18af)中，我试图向你介绍我的旅程:制作一款游戏(我忘了提到我想自己制作(或多或少)整个游戏，从引擎到资产)。我现在试图渲染*的*和*的*块...这比我想象的要难！

* * *

我在上一篇文章中简单提到了在 OpenGL (VAO，VBO，着色器)中玩 3D 渲染时需要的基本东西。从那开始，我试着用我的引擎添加一些代码来显示立方体:

```
glm::vec3 cubePositions[] = {
    glm::vec3( 0.0f,  0.0f,  0.0f), 
    // ..
};
zk::Vertices vertices({
    // a lot of things 
    // position: 3 floats, texcoords: 2 floats
    -0.5f, -0.5f, -0.5f,  0.0f, 0.0f,
    // ...
});

zk::objects::VertexArray VAO;
zk::objects::VertexBuffer VBO;
VAO.bind();
VBO.setData(vertices);
zk::objects::setVertexAttrib(0, 3, 5, 0);
zk::objects::setVertexAttrib(1, 2, 5, 3);

shader.use();
shader.setUniform1<int>("texture1", 0); 
```

Enter fullscreen mode Exit fullscreen mode

而且很管用！

[![Displaying a cube using my engine](img/6426273c282d2e669d7ab07b5dea275b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UAkgmv-H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6q9n3gw0lh5ndscurr5x.png)

但是过了一会儿，我意识到我这样做毫无用处:必须一个接一个地移动立方体会花费很长时间，所以我尝试了 4096 个立方体(它代表一个 16×16×16 的块，充满了块) :我得到了 **16.45** ms，大约 60 FPS。

我做的第一个优化是在绘制 x 个块时使用单个 VAO 和 VBO:我必须将它们的顶点合并到一个大的顶点数组中，以便在一个批处理中将其全部交给 OpenGL，并使用单个纹理和着色器，一次绘制它们全部(而不是告诉 OpenGL 使用块纹理，然后块着色器，...在我们的例子中是 4096 次):

```
namespace Chunk {
    std::size_t Width = 16, Height = 16, Depth = 16;
}

bool blocks[Chunk::Width][Chunk::Height][Chunk::Depth];
// "map generation"
for (int x=0; x < Chunk::Width; ++x)
{
    for (int y=0; y < Chunk::Height; ++y)
    {
        for (int z=0; z < Chunk::Depth; ++z)
        {
            // putting a block everywhere
            blocks[x][y][z] = true;
        }
    }
}

std::vector<float> temp_vertices;

// for each block, put vertices (moved by x, y, z for the position attributes)
// into temp_vertices 
```

Enter fullscreen mode Exit fullscreen mode

[![Display a chunk full of blocks](img/fcff9e8d515d08cb03cb3bdb977b6b5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X_AS8hVY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4x738kysx31io278jj3g.png)

每次更新新时间: **5.7** 牛逼女士！我们远低于 16.67ms 的极限！但我们仍然可以提高性能，因为我们显示隐藏的面，即每个块之间的面，它们是不相关的。

让我们隐藏所有的脸，如果有一个相邻的块:

```
if (x > 0 && blocks[x-1][y][z] == false)
{
    // add face back
    temp_mesh.update(getMeshFace(BlockFace::Back), x-1, y, z);
}
// and so on for the other faces 
```

Enter fullscreen mode Exit fullscreen mode

我添加了一个“getMeshFace”函数，能够获得描述立方体一个面的顶点，以简化隐藏过程。你也注意到我使用了一个`temp_mesh`变量。它是一个`Mesh`对象，操纵数据更容易:

```
struct Mesh {
    std::vector<float> vertices, normals, texcoords;

    void update(const Mesh& other, std::size_t x, std::size_t y, std::size_t z) {
        for (std::size_t i=0; i < other.vertices.size() / 3; ++i)
        {
            vertices.push_back(other.vertices[    i * 3] + x);
            vertices.push_back(other.vertices[1 + i * 3] + y);
            vertices.push_back(other.vertices[2 + i * 3] + z);
        }
        normals.insert(normals.end(), other.normals.begin(), other.normals.end());
        texcoords.insert(texcoords.end(), other.texcoords.begin(), other.texcoords.end());
    }

    std::size_t triangleCount()
    {
        return vertices.size() / 3;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

你还会注意到我添加了一个组件“法线”，我们稍后添加一些灯光时会用到它(基本上，法线组件指的是每个网格的法向量)。

每次更新时间: **2.7** 毫秒！我们做到了！正如你在封面上看到的，我隐藏了太多的面孔...希望上面的代码可以解决这个问题。

[![Rendering a weird chunk](img/bc52cf2625093efb054cfdd5b816c03d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FTi5x-gl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t279audzyp65twqltq32.png)

(不要担心某些地方的怪异纹理，这是由于我在纹理坐标方面的管理不善，我搞砸了一点，但它可以很容易地修复)。

我们设法:

*   将代表一个块的所有顶点一次性发送到 OpenGL
*   避免发送隐藏的面孔，以获得更好的性能

我们可以查看*视图截锥剔除*来只显示屏幕上看到的立方体，但是我不喜欢这样做，原因有二:

*   我的项目 Voksel 不需要大块编辑(所以我不需要每秒创建一个新的网格，这可能会花很多时间)，所以我不喜欢增加代码的复杂性，只是为了在更新时间上获得一些时间
*   这很难正确实现

如果你仍然有兴趣了解*视图截锥剔除*，我建议你阅读[这篇文章](http://www.lighthouse3d.com/tutorials/view-frustum-culling/)(来自 lighthouse3d)。

我们也可以实现*三角面合并*(我称之为 *TFM* )来获得更好的性能。TFM 是一种算法，将同一平面上的所有三角形分组，以创建一个大三角形，从而减少发送到 OpenGL 的顶点数量。这个算法可能需要很长时间来运行，尤其是如果你的块不是平的。

Fogleman 在他的 medium 页面上很好地解释了这种算法:[体素渲染技术，作者 Fogleman](https://medium.com/@fogleman/voxel-rendering-techniques-fa8d869457ca) 。

* * *

几行前，我写道“我添加了一个组件“法线”，我们以后添加一些灯光时需要它”，但是我没有使用它。我们将在下一篇文章中看到:)

PS:如果您想查看本系列文章的源代码，我为它创建了一个 [Gitlab 资源库](https://gitlab.com/SuperFola/devto_gl/)。