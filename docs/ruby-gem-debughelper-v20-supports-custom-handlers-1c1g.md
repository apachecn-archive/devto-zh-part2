# Ruby Gem debug_helper v2.0 支持定制处理程序

> 原文：<https://dev.to/burdettelamar/ruby-gem-debughelper-v20-supports-custom-handlers-1c1g>

红宝石宝石`debug_helper`[2.0 版](https://github.com/BurdetteLamar/debug_helper#new-in-version-20)上线了。

`debug_helper`通过显示关于对象的细节来帮助 printf 风格的调试，通常是递归的。

对定制处理程序的新支持:以前，助手支持 Ruby 核心和标准库中的选定类。

现在，您可以为自己的用户定义的类创建自定义处理程序。