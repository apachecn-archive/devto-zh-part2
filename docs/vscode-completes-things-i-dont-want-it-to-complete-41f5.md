# VScode 完成了我不希望它完成的事情。

> 原文：<https://dev.to/antonrich/vscode-completes-things-i-dont-want-it-to-complete-41f5>

点击回车键后>

[![VScode completes the syntax](img/172e7d18f6bd1a3fa68d2c2b1eeae83a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tjqK6c0x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhnagb6tlk7ka46q0jjp.png)

变成了这个>

[![VScode completed the syntax](img/6be2e113c9ae489645cd15ea3d738f9d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kIKdk-Q2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/48jub4amvop1p07c9one.png)

它实际上应该用缩进开始新的一行。

* * *

[![Let In expression in VScode](img/f573f2eacee4cbedeea6a5737a79deba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZL4cGizG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9hv55imwxkhinr4fm8pb.png)

当我按回车键时，它变成了这个>

[![weird VScode behavior](img/f67d42db7ee68aaf370a9c400b4c2f1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G613Ki1v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdb0xaruyyvmumgq5aad.png)

同样，它应该用缩进开始新的一行。

* * *

如何让 VScode 不换行？

由此>

[![](img/316aa8dad3ea1bbe65059dd7b7c7e393.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bJar1SLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pu2ttjebwxdih13c7261.png)

红线表示光标所在的位置。

到此>

[![](img/dfeb01606898ba340299015d06fbf244.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--td6SRZNW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l4su0jmmhqbc0xf1k2wz.png)

绿线表示我希望光标所在的位置，这样我就可以像这样键入代码>

[![](img/f17ccb2f2d76a60039481a53f22c16f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cNm4fm0M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e5uijnrko76hyqj2uku4.png)

* * *

输入问题的解决方案:

[![The solution to the enter problem with VScode](img/1650e4620c43dc6c790f6038237fd0cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4QPjZiAO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xl37jimyxz4eysbwhp7v.png)

转到设置。在搜索中键入“输入”，并关闭“输入时接受建议”。