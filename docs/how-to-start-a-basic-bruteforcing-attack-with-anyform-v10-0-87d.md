# 如何用任何形式的 1.0-0 版开始一个基本的暴力攻击

> 原文：<https://dev.to/yak0d3/how-to-start-a-basic-bruteforcing-attack-with-anyform-v10-0-87d>

在 [GitHub](https://github.com/yak0d3/anyForm/) 上查看 **anyForm v1.0-0** ！

# anyForm ![Language](../Images/a2200bdad7f032728f4b3d61a5269ca2.png)

[![GitHub tag (latest SemVer)](../Images/35309a78a811c7f7317d162240cbb5e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MMPgV2b8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/github/tag/yak0d3/anyForm.svg%3FcolorB%3Dbrightgreen)

> anyForm 是一个轻量级的表单暴力破解工具，可以破解任何包含用户名和密码字段的表单。

目前，anyForm 仍处于早期开发阶段，功能非常有限，但对于传统的 web 表单来说已经足够好了。

[![anyForm main display - screenshot1](../Images/5a9d9607eaeccf11514ac872e80d7744.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bbMDGe0J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://preview.ibb.co/d5KLwp/anyform_main_display.png)

# 用法

任何形式都是如此简单明了，你只需走过几步，你的强力攻击就万事俱备了。
按照以下步骤成功开始破解密码:

1.  `$ perl anyform.pl`
2.  `set url <your_form_url>`
3.  `set users <users_list>`
4.  `set passwords <passwords_list>`
5.  `set userField <field_name_from_html_source>`
6.  `set passField <field_name_from_html_source>`
7.  `set ssMsg <success_message_source>`
8.  `start` ![anyForm brute-forcing - Screenshot2](../Images/9c8b7fc59bebd42bcb04392bd024e94c.png)

# 命令

**如果你需要更深入地了解 anyForm，这里是 anyForm 命令列表:**

| 命令 | 行动 | 例子 |
| --- | --- | --- |
| 设置 url<value></value> | 将表单 url 设置为新值 | 设置网址[http://www.example.com/form1.php](http://www.example.com/form1.php) |
| 设置用户<value></value> | 给用户列表一个新值 | 设置用户 users_list.txt |
| 设置密码<value></value> | 赋予密码列表一个新值 | 设置密码 passwords_list.txt |
| 设置用户区域<value></value> | 输入用户字段的值(如 html | 设置用户字段用户名 |
| 设置通行字段<value></value> | 输入密码字段的值(如 | 设置用户字段密码 |
| 设置 ssMsg<value></value> | 设置要测试的成功字符串 | 设置 ssMsg success.txt |
| 开始 | 开始蛮力 |  |
| 帮助 | 显示此帮助消息 |  |
| 清楚的 | 清除控制台 |  |
| 出口 | 退出任意形式 |  |

## 待办事项:

这个版本的 anyForm 非常基础，以后的版本会有更好的功能，相信我，你会喜欢它的。
*以下是新增功能列表:*

*   [ ]自动安装缺少的模块。
*   [ ]添加自定义表单元素。
*   [ ]代理连接。
*   [ ]更好的交互界面。
*   [ ]多形式暴力破解。
*   [ ]数据生成器(用户、密码、令牌等...)

## 帮助提高 anyForm

你有新的想法吗？或者你认为任何形式都缺少了什么？只需提出新的拉动式请求或联系我@[contact.raedyak@gmail.com](mailto:contact.raedyak@gmail.com)提出修改建议。

## 报告一个错误

发生了一个错误，而您不知道是什么原因造成的？不要惊慌，如上所述，anyForm 仍处于早期开发阶段，可能会出现错误和 bug，只需制作新一期或联系我@【contact.raedyak@gmail.com】T2 @获取 bug 报告。

# 法律免责声明

*   ##### 我不承担任何责任，也不对使用本产品造成的任何损害负责。

*   ##### 我对任何非法使用不负任何责任。

    ## 执照

[![anyForm MIT License](../Images/649a66467f998afa1b1a1c58152a4d0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_72U_zMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/hWqzp9/license.png)