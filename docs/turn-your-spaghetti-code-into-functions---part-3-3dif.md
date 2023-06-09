# 将您的意大利面条式代码转化为函数——第 3 部分

> 原文：<https://dev.to/monknomo/turn-your-spaghetti-code-into-functions---part-3-3dif>

*原驻 www.gunnargissel.com*

从我们在第二部分中停止的地方继续[，让我们为使用验证器做好准备。我们把我们的例子大象放在一辆很酷的敞篷车里，但是让我们把他的坐骑升级到火箭船。我们将像所有优秀的火箭科学家一样开始一轮大扫除！](http://www.gunnargissel.com/turn-your-spaghetti-code-into-functions-part-2.html)

在 *[mise en place](https://en.wikipedia.org/wiki/Mise_en_place)* 之后，我们将准备实现一个`Validator`和`Result`，这将为业务规则创建一个单一的、标准的、可扩展的格式，以及一个用于消费业务规则查询结果的 api。不用再猜测符合规则的调用是什么样子了！不再将嵌套布尔逻辑的水晶塔串在一起！

## 清理

我们在几个地方使用了`.and()`来组合`if`块中的谓词。用一个独立的谓词替换它们，这样所有的`if`块只引用一个谓词

```
static final Predicate<WidgetTransfer> suffientAmount = trans -> trans.getTransferer().getAccount(trans.getFromAccount()).getBalance().compareTo(trans.getAmount()) < 0;
    static final Predicate<WidgetTransfer> isPartner = trans -> trans.getTransferTypeCode().equals("200");
    static final Predicate<WidgetTransfer> isFriendsAndFamily = trans -> trans.getTransferTypeCode().equals("710");
    static final Predicate<WidgetTransfer> isFriendAndFamilyDiscountLegal = trans -> trans.getAreaCode().matches("574|213|363|510");
    static final Predicate<WidgetTransfer> isPartneringArea = trans -> trans.getAreaCode().matches("907|412|213");
    static final Predicate<WidgetTransfer> isDirigibleForbiddenArea = trans -> trans.getAreaCode().matches("213");
    static final Predicate<WidgetTransfer> isDirigibleCategory = trans -> trans.getTransferer().getCategory().equals("D");
    static final Predicate<WidgetTransfer> isInternal = trans -> trans.getTypeCode().equals("I");
    static final Predicate<WidgetTransfer> isBlockSize = trans -> isBlockSize(trans);
    static final Predicate<WidgetTransfer> isTotalOverCap = trans -> isTotalOverCap(trans);

    static final Predicate<WidgetTransfer> parterTransferReqs = trans -> isPartner.and(isPartneringArea.negate()).test(trans);
    static final Predicate<WidgetTransfer> dirigibleTransferReqs = trans -> isPartner.and(isDirigibleForbiddenArea.negate()).and(isDirigibleCategory).test(trans);
    static final Predicate<WidgetTransfer> friendsAndFamilyReqs = trans -> isFriendsAndFamily.and(isFriendAndFamilyDiscountLegal.negate()).test(trans);
    static final Predicate<WidgetTransfer> internalBlockReqs = trans -> isInternal.and(isBlockSize).test(trans);
    static final Predicate<WidgetTransfer> internalTotalCapReqs = trans -> isInternal.and(isTotalOverCap).test(trans);

    public static final String checkWidgetTransfer(WidgetTransfer transfer) {
        String businessRuleErrors = "";

        if (suffientAmount.test(transfer)) {
            businessRuleErrors += "Insufficient balance to transfer ; ";
        }

        if (parterTransferReqs.test(transfer)) {
            businessRuleErrors += "This area is not a transfer eligible area. ; ";
        }

        if (dirigibleTransferReqs.test(transfer)) {
            businessRuleErrors += "D Category Transferer can only be transferred in transfer area 213\. ; ";
        }

        if (friendsAndFamilyReqs.test(transfer)) {
            businessRuleErrors += "This area is not a transfer eligible area. ; ";
        }

        if (internalBlockReqs.test(transfer)) {
            businessRuleErrors += "Amount is too small for I type transfer. ; ";
        }

        if (internalTotalCapReqs.test(transfer)) {
            businessRuleErrors += "This transfer is too large. ; ";
        }

        return businessRuleErrors;
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码有很多模板——但是普通 Java 开发人员看不到这些模板。那些`if`块都是锅炉板。你必须一遍又一遍地打字。如果我告诉你，你可以使用一个验证器来清理你所有的业务规则，会怎么样？想象一下您的业务代码中有几英里长的条件，想象一下每个条件都被简化为一个可组合的函数调用。

## 样板文件清除

[![literal boiler plat](img/6f6d94d5962a0051f65a8486f0abb5b6.png "literal boiler plate")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ghzjy8jx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/z5pXylbl.jpg)

另一个问题是`checkWidgetTransfer`返回一个字符串。这将确定错误是否发生的责任推给了调用方法。所有的`checkWidgetTransfer`调用者都需要一个如下所示的部分:

```
String result = checkWidgetTransfer(transfer);
if(null == result || 0 == result.size()) {
    //continue
}else{
    handleError(result);
} 
```

Enter fullscreen mode Exit fullscreen mode

将这个乘以会计部的 Bob、销售部的 Carol 和总部的 Duane 使用的每一个奇怪的过程。它可以得到....**大。**

通过使用`Validator`和`Result`技术，我们可以节省打字、共享条件和共享业务逻辑。从打电话者的角度来看，它是这样的:

```
checkWidgetTransfer(transfer).onError(err -> handleError(err));
//continue 
```

Enter fullscreen mode Exit fullscreen mode

这为调用者提供了一个 api，指示什么是错误条件。调用方不再需要猜测空字符串是通过，非空字符串是失败。api 提供了一个放置错误处理的地方，它可以采用一个现有的函数，或者有一个运行中的 lambda。太棒了。

你还得写出鲍勃、卡罗尔和杜恩最喜欢的工作流程。但事情是紧凑的，你不必走很多弯曲的分支，一个比一个更像。

### 现在有了验证器

[![rocket blasting off](img/55a559d34fb98116d2be1da716602d84.png "Blastoff!")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hLPDn-EK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/LxHru8gl.jpg)

如果您不必编写任何 if/then/else 语句，会怎么样？如果你只需要写逻辑，而其他东西会处理逻辑串在一起。验证器可以让这成为可能。

下面是使用验证器的`checkWidgetTransfer`的实现:

```
public static final Result<WidgetTransfer> checkWidgetTransfer(WidgetTransfer transfer) {
    Validator<WidgetTransfer> widgetTransferValidator = new Validator();
    widgetTransferValidator.addRule(suffientAmount, "Insufficient balance to transfer");
    widgetTransferValidator.addRule(parterTransferReqs, "This area is not a transfer eligible area");
    widgetTransferValidator.addRule(dirigibleTransferReqs, "D Category Transferer can only be transferred in transfer area 213");
    widgetTransferValidator.addRule(friendsAndFamilyReqs, "This area is not an eligible area");
    widgetTransferValidator.addRule(internalBlockReqs, "Amount is too small for I type transfer");
    widgetTransferValidator.addRule(internalTotalCapReqs, "This transfer is too large");
    return widgetTransferValidator.validate(transfer); 
} 
```

Enter fullscreen mode Exit fullscreen mode

验证器可以接受任意多的规则，每个规则都有一个名称和一条匹配的消息。验证器确保每个规则都应用于传输，并返回一个`Result`，包含错误消息或传输。

### 实现细节

[我实现了一个`Validator`](https://github.com/monknomo/If-Else-Block-Refactoring/blob/master/src/main/java/com/gunnargissel/suemez/businessrulerefactorexample/refactor5/Validator.java) 作为函数的`HashMap`来处理错误字符串。`validate`方法测试每个函数，如果测试为真，则在`Result`中收集匹配的消息。

[我将`Result`](https://github.com/monknomo/If-Else-Block-Refactoring/blob/master/src/main/java/com/gunnargissel/suemez/businessrulerefactorexample/refactor5/Result.java) 实现为`Either`。调用者可以选择获取一个布尔值`hasErrors`或者将一个`Consumer`传递给`onError`。关于`Result`重要的一点是，它从不为空，它指导开发人员找到处理错误的正确方法。

## 总结起来

只需轻轻一推，我们就能帮助大象进入轨道。我们已经从 发展到包含可组合业务逻辑的纯功能。谢谢你来兜风。我欢迎在 gunnar@gunnargissel.com 的[或 Twitter](mailto:gunnar@gunnargissel.com) 的 [@monknomo 发表评论或反馈](https://twitter.com/monknomo)

[![elephant in space, happy they have cleaned up code](img/c557a87890bba81181f615c8278d8ff6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--54GnDS3G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/5Xg3VrRl.jpg)

**[注册我的电子邮件列表，获取后续系列的更新通知，以及每月有趣的编程和技术领导文章摘要](http://www.gunnargissel.com/pages/email-signup-1.html)**

## 学分

*感谢[奥利佛·多德](https://www.flickr.com/photos/oliverdodd/)给了[大象](https://flic.kr/p/8N681r)T5】*

*感谢 [NASA、ESA、N. Smith (U. California，Berkeley)等人，以及哈勃遗产团队(STScI/AURA)](https://www.nasa.gov/multimedia/imagegallery/image_feature_1146.html) 对船底座星云的贡献*

*[奋进号](https://flic.kr/p/fejqBW)航天飞机照片[小手先生](https://www.flickr.com/photos/73577218@N00/)T5】*

*[锅炉板块](https://flic.kr/p/Fvyd) [来自莱斯查特菲尔德](https://www.flickr.com/photos/elsie/)*

*[升空](https://flic.kr/p/q5sdna)乘[马修·兰卡斯特](https://www.flickr.com/photos/matthew_lancaster_2/)T5】*