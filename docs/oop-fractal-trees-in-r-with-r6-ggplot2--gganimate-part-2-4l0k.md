# 用 R6、ggplot2 和 gganimate 设计 R 中的分形树(第二部分)

> 原文：<https://dev.to/adamspannbauer/oop-fractal-trees-in-r-with-r6-ggplot2--gganimate-part-2-4l0k>

这篇文章是第 2 部分，如第 1 部分所述，最终目标是用 [`R6`](https://r6.r-lib.org/) & [`gganimate`](https://github.com/thomasp85/gganimate) 创建一个动画分形树。今天，我们将从[第一部](https://dev.to/adamspannbauer/oop-fractal-trees-in-r-with-r6-ggplot2--gganimate-part-1-19bm)到 [`gganimate`](https://github.com/thomasp85/gganimate) 制作`fractal_tree` [`R6`](https://r6.r-lib.org/) 类动画。

下面的代码和图表显示了在这篇文章的结尾我们将会得到什么。

```
# Create & animate R6 tree object  tree  =  fractal_tree_seq$new()  tree$animate() 
```

[![](../Images/464af4d6dcf28298122913a100351c5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3dp1kmjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ivsxrlx5lsgs5o06x69c.gif)

***注**:本帖意在探究 [`R6`](https://r6.r-lib.org/) 功能；这并不是创造分形树的最好方法。一些设计选择仅仅是为了利用不同的功能。另外，这篇文章更多的是基于例子而不是解释。更深入的解释，我推荐从`R6`进入[这一页](https://r6.r-lib.org/)或者从[高级 R](https://adv-r.hadley.nz)*
查看[这一章](https://adv-r.hadley.nz/r6.html)

* * *

## 设计

[![](../Images/11a4aa2e527853f8d3078215d9a106a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--toZ7WxWb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zpndgviot59elezyo36.png)

我们已经有了一个`fractal_tree`对象，它可以创建一棵带有以给定角度放置的分支的树。为了简单起见，我们将构建我们的动画树作为我们的(已经定义的)`fractal_tree`对象的序列；每棵树都是动画中的一帧。免责声明:如果您最终运行了这段代码，您会发现这种方法可能不是最有效的方法，但它是有效的。

## 实现

***注意:**这篇文章的代码假设来自[第 1 部分](https://dev.to/adamspannbauer/oop-fractal-trees-in-r-with-r6-ggplot2--gganimate-part-1-19bm)的对象被加载到你的 R 会话中。第一部分[的完整代码](https://dev.to/adamspannbauer/oop-fractal-trees-in-r-with-r6-ggplot2--gganimate-part-1-19bm)可以在[这里](https://gist.github.com/AdamSpannbauer/6aef835e1784a74b56b709c6159d2ca2)T9】找到*

坚持 2 部分系列的主题，我们将创建一个单独的 [`R6`](https://r6.r-lib.org/) 类来存放我们的动画过程。上面的“设计”部分可能看起来是在很高的层次上编写的，但是它几乎涵盖了我们将在下面讨论的所有实现细节。

### `fractal_tree_seq`

做动画的对象被命名为`fractal_tree_seq`,因为它只是一系列的`fractal_tree`。在对象的`initialize`方法中，我们循环用户提供的`angle_seq`,并在序列的每个角度创建一棵树。此外，当我们创建每棵树时，我们分配一些元数据来显示该树属于哪个帧。最后，在我们的`initialize`方法中，我们给每个角度分配一种颜色，这个信息将被用于绘制给我们的动画一些闪光。

为了结束我们的`fractal_tree_seq`类，我们添加了一个`public` `animate`方法，它看起来很像第 1 部分中的 plot 方法。 [`gganimate`](https://github.com/thomasp85/gganimate) 的语法与 [`ggplot2`](https://ggplot2.tidyverse.org/) 的非常相似，所以体验了以后应该会对`animate`代码感到熟悉。我们添加到 [`ggplot2`](https://ggplot2.tidyverse.org/) 表达式中的 [`gganimate`](https://github.com/thomasp85/gganimate) 代码的唯一位是`+ transition_manual(frame)`。该命令将使用我们在`initialize`中分配的帧数据来创建我们的`fractal_tree`的 gif。

就是这样！我们实现了用[`R6`](https://r6.r-lib.org/)[`gganimate`](https://github.com/thomasp85/gganimate)创建并动画制作分形树的目的。

```
fractal_tree_seq  =  R6Class('fractal_tree_seq',  public  =  list(  trees  =  data.frame(),  initialize  =  function(trunk_len  =  10,  angle_seq  =  seq(0,  2  *  pi  -  pi  /  32,  pi  /  32),  len_decay  =  0.7,  min_len  =  0.25,  verbose  =  TRUE)  {  total  =  length(angle_seq)  for  (i  in  seq_along(angle_seq))  {  if  (verbose)  cat(sprintf('creating tree %s of %s\n',  i,  total))  angle  =  angle_seq[i]  tree_i  =  fractal_tree$new(trunk_len  =  trunk_len,  delta_angle  =  angle,  len_decay  =  len_decay,  min_len  =  min_len)  branches_i  =  tree_i$branches  branches_i$angle  =  angle  branches_i$frame  =  i  self$trees  =  rbind(self$trees,  branches_i)  }  angle_colors  =  data.frame(angle  =  sort(unique(self$trees$angle)))  angle_colors$angle_color  =  rainbow(nrow(angle_colors))  self$trees  =  merge(self$trees,  angle_colors,  all.x  =  TRUE,  by  =  'angle')  },  # initialize  animate  =  function()  {  ggplot(self$trees,  aes(x,  y,  group  =  id))  +  geom_line(aes(color  =  branch_color))  +  geom_point(size  =  .2,  aes(color  =  angle_color))  +  scale_color_identity()  +  guides(color  =  FALSE,  linetype  =  FALSE)  +  theme_void()  +  transition_manual(frame)  }  )  # public  )  #fractal_tree_seq 
```

## 最终产品

这最后一节将是使用我们的`fractal_tree`类的功能的几个例子。

```
# Create & animate R6 tree object  tree  =  fractal_tree_seq$new()  tree$animate() 
```

[![](../Images/464af4d6dcf28298122913a100351c5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3dp1kmjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ivsxrlx5lsgs5o06x69c.gif)

```
# Create & animate R6 tree object with new min_len  tree  =  fractal_tree_seq$new(min_len  =  3)  tree$animate() 
```

[![](../Images/09334d37e4a19fa55ffcf595fc01855d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gVzi_K7S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4taglrchquz72m1ahl13.gif)

```
# Create & animate R6 tree object with new angle_seq  tree_seq  =  fractal_tree_seq$new(angle_seq  =  runif(4,  min=pi  /  16,  max=pi  /  4))  tree$animate() 
```

<sub>**变小以来就这么晃眼*</sub>

[![](../Images/0f483831ae943bd38c57fc03cffeb086.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W3fcoATx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7eubcqpu2j5kjqdo1pcs.gif)