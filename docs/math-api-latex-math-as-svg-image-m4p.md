# 数学 API: LaTeX Math 作为 SVG 图像

> 原文：<https://dev.to/uetchy/math-api-latex-math-as-svg-image-m4p>

我一直想把 LaTeX 数学方程放在一个网页上，而 MathJax 是不允许在里面运行的。

花了一些时间，我制作了 [Math API](https://math.now.sh) ，它将 LaTeX 数学标记渲染成一个 SVG 图像。

所以你可以把你的方程式放在任何你可以放置`<img>`或`![]()`的地方，比如 GitHub、Jupyter Notebook 或 dev.to(这里！).

```
![](https://math.now.sh?from=\\LaTeX) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/4833d5495cdfa391065401afaf9d636d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--smAy_2Jr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://math.now.sh%3Ffrom%3D%255CLaTeX)T3】

```
![](https://math.now.sh?from=\\log\\prod^N_{i}x_{i}=\\sum^N_i\\log{x_i}) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/62bdbc2fb4b847c871b8ea7a9a548b21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aVmJ5Mwv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://math.now.sh%3Ffrom%3D%255Clog%255Cprod%255EN_%257Bi%257Dx_%257Bi%257D%3D%255Csum%255EN_i%255Clog%257Bx_i%257D)

# 内嵌图像

[![](img/878770557d7fd7a8db7b874393fb860d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HMTqtvTp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqea9nq2wv9in15lqlf3.png)

[![](img/99b1dafabc162b167ddbb5e0cb064da8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9Yd_rUJ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43slt0h6dfhox1xwmuti.png)

通过将查询从`from`更改为`inline`，可以生成一个内嵌方程。

```
<img src="https://math.now.sh?inline=\\LaTeX" /> 
```

Enter fullscreen mode Exit fullscreen mode

# 在线编辑

另外，在 [https://math.now.sh](https://math.now.sh) 上有在线编辑器。

[![](img/545091b8b47df4a4bc0bc017c4191a06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vWP7jtO---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gg2wil3exu9lyj7ppuoy.png)

# 结论

源代码可以在 [GitHub](https://github.com/uetchy/math-api) 上获得。
试试看，给新功能留下评论/想法。