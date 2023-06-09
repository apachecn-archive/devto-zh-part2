# 当领域术语发生变化时，您应该在多大程度上重构名称？

> 原文：<https://dev.to/hudsonburgess7/how-much-should-you-refactor-names-when-domain-terminology-changes-k73>

这很难抽象地解释，所以这里有一个假设的场景:

您正在为一家销售电脑显示器的商店构建一个管理库存的应用程序。有一天，商店决定以后显示器将被称为“显示器”您的经理会在与您的谈话中迅速承诺这一术语变化，营销团队会确保他们的所有内容也反映这一变化。对该公司来说，术语的这种变化很重要。

作为一名开发人员和软件工匠，您应该:

*   将代码中所有出现的`monitor`重构为`display`(在类名、变量名、文件夹结构等中。)保持术语一致？
*   不要管名字，因为只有开发人员会看到它们，术语的不匹配可能不会引起任何混淆？
*   还有别的吗？

当领域术语发生变化时，您应该在多大程度上重构名称？