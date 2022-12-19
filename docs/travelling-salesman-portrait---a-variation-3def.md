# 旅行推销员肖像——一种变体

> 原文：<https://dev.to/jacoby/travelling-salesman-portrait---a-variation-3def>

这要从我看到兰迪·奥尔森发现了一个有趣的东西并在此基础上发展开始。

[Python 中的旅行推销员画像](http://www.randalolson.com/2018/04/11/traveling-salesman-portrait-in-python/)就是我进来的地方。它通过一系列步骤运行:

*   举个例子，波利斯·卡洛夫是弗兰肯斯坦的怪物
*   把它变成灰度
*   抖动它，让它更完全的黑白
*   拉一些随机选择的黑色像素
*   运行旅行推销员算法来连接所有的像素
*   画线并写出图像

问题是，戴夫·雅各布和 Python 就像是[查理·布朗和一只风筝](https://www.google.com/search?q=charlie+brown+kite&oq=charlie+brown+kite)；我永远无法让它工作，最后我被打败了，被绑在一棵树上。在这种情况下，相信这是因为 matplotlib 不喜欢我处理字体的方式，这是无关紧要的，因为在这个过程中没有涉及字体。

( **#VirtualEnv 所有的事情**)

但是兰迪·奥尔森自己并没有想到这一点，他把它从 R 改编成 Python，并以[弗隆克斯汀的作品](https://fronkonstin.com/2018/04/04/the-travelling-salesman-portrait/)为基础。我对 R 没有什么问题。我的主要问题是，我发现很难从 R 的数据数组等方面进行思考。而我的`ggplot2`知识是*所以*货崇拜。但我可以用代码工作。

```
#!/usr/bin/env Rscript
# https://github.com/aschinchon/travelling-salesman-portrait/blob/master/frankenstein_TSP.R

# to be done:
# replace the library() calls with something quieter
# make usage -i file -o file
# remove the urlfile part (?)
# use Cairo so I can batch it

# Quietly load modules
suppressPackageStartupMessages(require("methods", quietly=TRUE))
suppressPackageStartupMessages(require("imager", quietly=TRUE))
suppressPackageStartupMessages(require("dplyr", quietly=TRUE))
suppressPackageStartupMessages(require("ggplot2", quietly=TRUE))
suppressPackageStartupMessages(require("scales", quietly=TRUE))
suppressPackageStartupMessages(require("TSP", quietly=TRUE))

# Handle Command Line Arguments
# cmd -> error ('need arguments')
# cmd file -> error if ! -f file, input = file, output = 'output.jpg'
# cmd file1 file2 -> error if ! -f file1, input = file1, output = file2
args = commandArgs(trailingOnly=TRUE)
if (length(args)==0) {
  stop("At least one argument must be supplied (input file).", call.=FALSE)
} else if (length(args)==1) {
  # default output file
  args[2] = "./output.jpg"
}
input=args[1]
output=args[2]

file=input
if (!file.exists(file)) { stop ("No valid input file", call.=FALSE) }

# Load, convert to grayscale, filter image (to convert it to bw) and sample
load.image(file) %>% 
  grayscale() %>%
  threshold("45%") %>% 
  as.cimg() %>% 
  as.data.frame() %>% 
  sample_n(8000, weight=(1-value)) %>% 
  select(x,y) -> data

# Compute distances and solve TSP (it may take a minute)
as.TSP(dist(data)) %>% 
  solve_TSP(method = "arbitrary_insertion") %>% 
  as.integer() -> solution

# Create a dataframe with the output of TSP
data.frame(id=solution) %>% 
  mutate(order=row_number()) -> order

# Rearrange the original points according the TSP output
data %>% 
  mutate(id=row_number()) %>% 
  inner_join(order, by="id") %>% arrange(order) %>% 
  select(x,y) -> data_to_plot

# A little bit of ggplot to plot results
ggplot(data_to_plot, aes(x,y)) +
    geom_path() +
    scale_y_continuous(trans=reverse_trans())+
    coord_fixed()+
    theme_void()

# Do you like the result? Save it! (Change the filename if you want)
ggsave(output, dpi=600, width = 4, height = 4) 
```

Enter fullscreen mode Exit fullscreen mode

这让我可以做这样的事情:

`./tsp.R Frankenstein.jpg TSP-Frankenstein.png`

得到这个输出。

[![My TSP Frankenstein](../Images/ad304793306d532f7cace9f9d61ff412.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ihuXZnJq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jacoby.github.io//images/TSP-Frankenstein.png) 。

对...做同样的事

`./tsp.R headshot.jpg TSP-headshot.png`

听听这个

[![My TSP Headshot](../Images/73f50e21c4b1d4d0dfbb0b240d9d3cbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TH6LeT14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jacoby.github.io//images/TSP-headshot.png) 。

但是我不喜欢这样，你可以在漫画里看到。我认为这是因为“真正的 R 用户”把它当作一个数据外壳，而我喜欢把事物想象成程序。我得到类似格式的数据(比如来自数据库的数据)，在我的 R 中批量运行，在我做其他事情的时候得到输出。

作为那种用户，我真的希望这是真的

`./tsp.R --input headshot.jpg --output TSP-headshot.png`

带有可选的阈值和线宽标志。我不知道 Perl 的 [Getopt::Long](https://metacpan.org/pod/Getopt::Long) 的 R 等价物。我可能不得不写一个，如果只是为了我自己的启迪。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。