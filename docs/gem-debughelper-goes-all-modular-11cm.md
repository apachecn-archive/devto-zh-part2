# Gem debug_helper 完全模块化

> 原文：<https://dev.to/burdettelamar/gem-debughelper-goes-all-modular-11cm>

根据用户的建议，我对 Ruby gem `debug_helper`进行了改进，这样它既可以作为类使用，也可以作为模块使用。

根据建议，新的模块方法`putd`具有方法`puts`的外观和感觉。

现在，您可以使用这些方法中的任何一种来分析对象(使用适当的`require`和`include`):

*   上课方法:`DebugHelper.show(object, message)`
*   模块方法:`putd object, message`

链接:

*   GitHub debug_helper [自述](https://github.com/BurdetteLamar/debug_helper#debug-helper)。
*   关于用作[模块](https://github.com/BurdetteLamar/debug_helper#class-method-or-module-method)的章节。
*   RubyGems [debug_helper](https://rubygems.org/gems/debug_helper) 。