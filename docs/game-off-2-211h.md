# 游戏关 2018 (2)

> 原文：<https://dev.to/bigaston/game-off-2-211h>

你好！【2018 我的游戏关报告第二天！

今天我继续为我的游戏做像素艺术！

我创作了一些花...
[![Flowers](img/2564ce678f7d646154edc5c0c749a72c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--BkL-peOJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ddv340xfvokzoapx1o1.gif)

和一些种子...
[![Seeds](img/dee2148acfda87100710d2861bed633b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--dtG2WhZy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5w7ipn8opcapw8uqodie.gif)

我不知道哪种花能和其他的杂交，但是现在我有一些艺术！

我已经开始写一些函数来处理一些 2D 表了！

```
function array(pRow, pCol, pDefault)
    local tab = {}

    for row=1, pRow do
        local tab_col = {}
        for col=1, pCol do
            table.insert(tab_col, pDefault)
        end
        table.insert(tab, tab_col)
    end

    return tab
end

function access(pRow, pCol, pTab)
    local col = pTab[pRow]
    local val = col[pCol]
    return val
end

function replace(pRow, pCol, pTab, pVal)
    pTab[pRow][pCol] = pVal
end 
```

都是为了今天！我想我会每天分享我的进步！祝你有愉快的一天！