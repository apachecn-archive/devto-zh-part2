# 我创建了一个 chrome 扩展来将随机数据粘贴到表单字段中🎷🥁

> 原文：<https://dev.to/ppshobi/i-have-created-a-chrome-extension-to-paste-random-data-to-form-fields--476j>

嘿伙计们，

> 前两天我在做 chrome 的扩展，它可以在表单字段中插入假数据。最后我在 chrome 网上商店发布了它，你可以在⚡️ [这里](https://chrome.google.com/webstore/detail/random-text-generator/lmealcdbihjhocippaajjhijhjciaojo) ⚡️查看

我花了几天时间修改打字稿，然后我想是的。我应该用打字稿做点什么。🤠

有一天，我的同事在测试一个需要填写大量表格的功能，他从一个网站复制 lorem ipsum 并将其粘贴到表单字段中。我告诉他了，伙计...你可以做得更好，检查一个 chrome 扩展，你可以很容易地填写表格进行测试。

他进行了搜索，找到了一些对他有帮助的扩展。但是，复制粘贴部分仍然涉及到，(即:你点击扩展图标，它会给出一个弹出窗口，你需要从那里复制并粘贴到表单中。)这当然不符合标准。

此外，不支持像信用卡、姓名、地址、邮政编码等数据...是的，☃️，我有了一个 chrome 扩展的想法，我可以尝试用 typescript 来做，学习 chrome API 也很好。这么...这么...

我把 faker.js 作为一个起点，它可以根据需要生成几乎任何类型/数量的假数据，它也是一个被广泛接受的用于单元测试的库。

但是随着项目的进展，花了整整两个晚上才弄清楚复杂的打字稿和 chrome 扩展架构，然后我就想，好吧...与其进行全面的扩展，不如让我创建一个版本，它可以给出给定长度的随机文本并发布测试版。然后，我可以继续添加功能。

今天我做到了🍾🍾🍾

所以此时，安装完扩展后，需要按
`Alt + Shift + I`，然后在提示框中给出一个长度。您最后关注的输入字段将被随机数据填充。

**扩展网址**-[https://chrome . Google . com/web store/detail/random-text-generator/lmealcdbihchocippajhijhjciaojo](https://chrome.google.com/webstore/detail/random-text-generator/lmealcdbihjhocippaajjhijhjciaojo)
T5】Github Repo-[https://github.com/ppshobi/chrome-random-text-generator](https://github.com/ppshobi/chrome-random-text-generator)% }

**所以伙计们...请测试一下。让我知道这里或 github 问题页面上的问题。如果你愿意贡献，我也很乐意指导你。❤️❤️❤️**

#### 简短的演示视频

[https://www.youtube.com/embed/_sGQypdmLXc](https://www.youtube.com/embed/_sGQypdmLXc)