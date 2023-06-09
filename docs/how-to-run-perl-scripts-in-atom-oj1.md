# 如何在 Atom 中运行 Perl 脚本

> 原文：<https://dev.to/aaronspitalny/how-to-run-perl-scripts-in-atom-oj1>

安装 Atom 包 Atom Runner。
[![](img/b0c9cdfb016956da9c6cc951edc837f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pVlt7xqx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ktq5ekvq1o6r4f7lm7o8.png) 

#### **一旦安装了 Atom Runner，运行 Perl 脚本有两种选择。**

 **#### 第一个选项:使用 shebang 行** 
[![](img/ca7b78646b51c16032d95c1314edff8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HtPqovr9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4stoi9mueq6g4j8w9r2f.png)
 **#### 第二个选项:编辑 Atom 配置文件** 
打开 config.cson 文件
[![](img/23958ba8beb4ca47ed89935004521b89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JqESOHpa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m6sqfayxzex35z1v1mrm.png) 
并插入
[![](img/7fb34b013f08bcb0cb2f87e537aed90a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8vCucVeJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yq9gmgx0kewe6jiz80t8.png)

```
runner:
  extensions:
    pl: "perl"

```

### **最后:**

使用按键 ctrl-r 和关闭你去！
[![](img/e1a3864866c02ca40bb767875f2d5156.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--IkC5YT_l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43unf6904lntoseb4b5n.png)