# chessBoardColorCalculator

> 原文：<https://dev.to/stanzheng/chessboardcolorcalculator-22m3>

我的这个码斗问题解决方案
[https://app . code signal . com/arcade/intro/level-6/t 97 bpjfrmdzh 8 gjhi/description](https://app.codesignal.com/arcade/intro/level-6/t97bpjfrMDZH8GJhi/description)

生成奇数和偶数偏移板，并检查它们的参考值

> 给定标准棋盘上的两个单元格，确定它们是否有相同的颜色。

```
def chessBoardCellColor(cell1, cell2):
    [fooa, foob] = cell1
    [bara, barb] = cell2

    fooa = ord(fooa) % 2 
    foob = int(foob) % 2

    bara = ord(bara) % 2 
    barb = int(barb) % 2

    board = [[(i+1)%2 for i in range(0,9)], [i%2 for i in range(0,9)]]

    return True if board[fooa][foob] == board[bara][barb] else False 
```

Enter fullscreen mode Exit fullscreen mode