# 日期::假期 1.08 发布

> 原文：<https://dev.to/jonasbn/dateholidays-108-released-18lf>

我刚刚上传了[日期::假期](https://github.com/jonasbn/perl-date-holidays) [1.08](https://metacpan.org/release/JONASBN/Date-Holidays-1.08) 到暂停/CPAN。

它为[Date::Holidays::US federal](https://metacpan.org/pod/Date::Holidays::USFederal)(US)保存了一个新的适配器类，以响应用户的请求。

由于改编的 Date::Holidays 类名 Date::Holidays::USFederal 中的变化，实现需要对内部代码进行大量更改，这不是 ISO 兼容的国家代码。我也在考虑支持我们。

与此同时，测试套件被重新构建。我希望我没有破坏任何东西，目前所有的测试都通过了本地和 Travis 通过 GitHub。我把很多测试从*。t 文件转换成基于 [Test::Class](https://metacpan.org/pod/Test::Class) 的实现，我发现这样更容易操作。

上传时，我确实观察到索引失败:

```
Status: Permission missing
==========================
  module : Date::Holidays::Adapter::Local 
  version: 1.08 
  in file: lib/Date/Holidays/Adapter/Local.pm 
  status : Not indexed because of case mismatch. 
```

我不确定这个错误意味着什么，但我会调查这是否引起了任何问题或给并发症。该类之前被称为 Date::Holidays::Adapter::LOCAL，所以这可能是问题的原因。

CPAN 的测试人员报告了很多失败的测试，我也会对此进行调查([第 21 期](https://github.com/jonasbn/perl-date-holidays/issues/21))。我不怀疑索引问题是相关的，目前我可以看到这似乎是发行版中测试类的包含路径(@INC)的问题，但我无法用 prove、make test(基于 Makefile)重现该问题。PL)或者。/Build 测试(基于 Build。PL)。