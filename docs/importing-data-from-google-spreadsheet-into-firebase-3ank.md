# 将数据从 Google 电子表格导入 Firebase

> 原文：<https://dev.to/tiptopgs/importing-data-from-google-spreadsheet-into-firebase-3ank>

对于一个小组项目，我们决定使用 Google 表单收集资源，将数据存储到电子表格中。为了让它对我们更有用，我们决定将数据导入 Firebase。以下是将数据从 Google 电子表格导入 Firebase 的步骤。

#### 电子表格设置

1.  制作电子表格的副本
2.  在工具菜单上，选择*脚本编辑器…* 选项

[![](../Images/27b9f070ee27dd19ac013bf7c50fd640.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bcsdkIRf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATqxHjaNyDHduIBhyThDsSg.png)

1.  进入脚本编辑器后，选择资源>库…

[![](../Images/f5af0514d95178f72ec2b215e0f744a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2579xm76--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/530/1%2AeUmJ7J9eUabiE2GuW3yVyw.png)

1.  按照这里的步骤选择 Firebase 库:[https://sites . Google . com/site/scripts examples/new-connectors-to-Google-services/Firebase](https://sites.google.com/site/scriptsexamples/new-connectors-to-google-services/firebase)

[![](../Images/0a7cf7081712d73b4aa6d961f25f4309.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zcSiYwYO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbEo6nD6ZXMh5xeSz9OObQw.png)

1.  按照这里的步骤开始将数据导入 Firebase:[https://sites . Google . com/site/scripts examples/new-connectors-to-Google-services/Firebase/tutorials/read-and-write-data-in-Firebase-from-apps-script](https://sites.google.com/site/scriptsexamples/new-connectors-to-google-services/firebase/tutorials/read-and-write-data-in-firebase-from-apps-script)

将电子表格数据导入 Firebase 时有以下注意事项:

Firebase 键不能有无效字符，所以请看下面的字符集限制。密钥是用于构建 Firebase 引用的 URL 的一部分。因此，作为一种变通方法，将电子邮件地址编码为 base64 就可以了。

**字符集限制**

请注意，用于构造 Firebase 引用的 URL 可以包含任何 unicode 字符，除了:

*   。(句号)
*   $(美元符号)
*   [(左方括号)
*   ](右方括号)
*   #(井号或井号)
*   /(正斜杠)

在对电子表格运行应用程序脚本之前，必须提供权限。

最后，将数据从电子表格导入 Firebase 的应用程序脚本如下所示: