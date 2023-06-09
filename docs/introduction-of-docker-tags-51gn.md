# Docker 标签介绍

> 原文：<https://dev.to/shubheksha/introduction-of-docker-tags-51gn>

<figure>[![](img/117ad9d080586a2e13fc4767f7d47ab4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BLta44-z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AKBn45TeUMJZSbz9n.png) 

<figcaption>[功劳](https://logz.io/blog/what-is-docker/)</figcaption>

</figure>

如果你和 Docker 合作过一点点，我敢打赌你一定遇到过标签。它们通常看起来像“my_image_name:1 ”,其中冒号后面的部分称为该标签。在标记图像时并不总是指定，我们稍后会深入了解。

自从开始使用 Docker，我就对标签非常困惑。医生对理解它们没有帮助。关于这个话题也没有详尽的解释。这就是我决定写这篇文章的原因。

### Docker 标签是什么？

那么…标签到底是什么？简单地说，标签只是图像 ID 的别名，通常如下所示:f1477ec11d12。只是一种参照自己形象的方式。一个很好的类比是 git 标签如何引用历史中的一个特定提交。

标签发挥作用的两种最常见的情况是:

1.  构建映像时，我们使用以下命令:

```
docker build -t username/image_name:tag_name . 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着解释一下这个命令的作用。我们告诉 Docker 守护进程获取当前目录中的 Docker 文件(即。末了确实如此)。接下来，我们告诉 Docker 守护进程构建映像并给它指定的标记。现在，如果您运行 docker images，您应该会看到一个映像，它的存储库是 username/image_name，标记是 tag_name。

用户名/图像名称不是指定图像名称的强制格式。这只是一个有用的约定，以避免当您需要将图像推送到注册表时再次标记您的图像。你的形象可以被命名为任何你想要的。对于公共 Docker 注册表，在命名图像时，您被限制为两级层次结构，也就是说，您的图像不能有名称 a/b/c:1。这种限制在私有注册中心通常不存在。如前所述，指定 tag_name 不是强制性的。我们很快就会看到在那种情况下会发生什么。

1.  通过 tag 命令显式标记图像:

```
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG] 
```

Enter fullscreen mode Exit fullscreen mode

该命令只是根据引用源映像的目标映像的名称创建一个别名(引用)。这就是它的全部功能。这就像给现有的图像指定另一个名称来引用它。注意标签在这里是如何被[:标签]指定为可选的。

### 不指定标签会怎样？

好了，现在让我们来看看当你在标记一张图片时没有指定标签会发生什么。这就是最新标签出现的地方。每当图像被标记而没有显式标记时，默认情况下会被赋予最新的标记。这是一个不幸的命名选择，引起了很多混乱。但是我喜欢把它看作是当你没有指定图片时给图片的默认标签。

由于期望它是映像的最新版本，尤其是在 docker 文件中，导致了许多关于 latest 的混淆。让我们通过一个示例来考虑各种场景:

#### 场景一:

假设我们的 docker 文件中有以下语句:

```
FROM debian 
```

Enter fullscreen mode Exit fullscreen mode

由于我们没有指定任何标签，Docker 将添加最新的标签，并尝试拉取 imagfe debian:latest。

#### 场景二:

```
FROM debain:9.3 
```

Enter fullscreen mode Exit fullscreen mode

由于这里明确提到了标记，Docker 将提取标记为 9.3 的 Debian 映像

另一件要记住的事情是，没有规则规定一个图像只需要一个标签。一个图像可以有多个标签，它们通常用于指定主要版本和次要版本。例如，考虑以下情况:

<figure>[![](img/0f6e4f214bf59c697c0a1d7feec339db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3goAPxsR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/682/1%2AEVve3i5XWD1a1ZwbBLoCrg.png)

<figcaption>[【debian 坞站枢纽页面】](https://hub.docker.com/r/library/debian/)</figcaption>

</figure>

在撰写本文时，Debian 映像的最新标签指向 9.3 版本和 9 版本。这种情况很可能会在将来映像的主版本或次版本发生变化时发生。

请注意，用于语义版本控制的标签是一个遵循的惯例，但是标签不是为了这个目的而设计的*而仅仅是*。

### 在结论中，最新的不是特殊的标记

到目前为止，我们所讨论的主要内容是: **latest 就像任何其他标签**一样。开发人员有责任正确地标记图像，以便 latest 总是指向图像的最新稳定版本。

因此，如果在提取图像时不在 docker 文件中显式指定标签，下次我们提取图像时，我们可能会得到与之前使用的基础图像完全不同的版本。没有人能保证这是一个大的颠簸还是一个小的颠簸。即使是旧版本也可以标记为最新版本。

附:如果你在帖子中发现任何误解/错误，请随时发推文给我 [@ScribbingOn](https://twitter.com/ScribblingOn) 。

感谢 jérme Petazzoni 帮助我理解了这些。