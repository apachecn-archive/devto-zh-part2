# 假人的概念:第 2 部分:概念

> 原文：<https://dev.to/kaisershahid/codecept-for-dummies-part-2-concepts-1idk>

在…里

[![kaisershahid image](../Images/259e3db1bdd497ed8bac88195a792398.png)](/kaisershahid) [## 假人的概念代码:第 1 部分:概述

### 凯撒沙希德 3 月 21 日 183 分钟阅读

#php #testing #codecept](/kaisershahid/codecept-for-dummies-part-1-overview-4bme)I went over test basics and how to setup an empty Codecept project (for lack of a better term).

现在我要谈谈 Codecept 的测试方法。理解这一点将是构建实际测试的关键。

## 演员(单位、职能、有偿索罗斯打手，随便)

测试是通过**参与者**的视角来执行的。演员有一定的期望:

*   *我想得到[http://www.google.com](http://www.google.com)T3】的内容*
*   *我想验证是否有搜索框*
*   *我想查一下`square(2) == 4`*

就代码而言，它实际上可能是这样的:

*   `$html = $I->wantContentFromUrl('http://www.google.com')`
*   `$I->wantToFindElement("input[title='Search']", $html)`
*   `$I->assertEquals(4, square(2), "unexpected value for square(2)");`

每个测试套件都有一个唯一的参与者(例如`FunctionalTester`)。

## 帮手

助手是 [Codecept 模块](https://codeception.com/docs-2.0/03-ModulesAndHelpers)——它们是包含参与者可以使用的方法的类。每个测试套件都可以定义加载到角色中的助手。

> 实际上，Codecept 将生成一个包含所有启用的帮助器的所有方法的`trait`，并将每个方法代理给实际实现它的类。actor 类然后使用那个`trait`。

## 切普斯

这是最基本的测试类型。基本上只是一个脚本:

```
I am on page 'http://www.google.com'
I see element 'input[title="Search"]'
I set value to 'input[title="Search"]', 'kaiser'
I click 'input[value="Google Search"]'
I get sent to 'https://www.google.com/search?q=kaiser' 
```

Enter fullscreen mode Exit fullscreen mode

(顺便说一句，你实际上可以这样写概念。)

> 在这个例子中，我们假设`I am on page`将 HTML 保存在其他步骤可以引用它的地方。

如果你需要更好的控制，你可以把它转换成 PHP:

```
<?php
$I = new AcceptenceTester($scenario);
$I->amOnPage('http://www.google.com');

$searchEle = 'input[title="Search"]';
$I->seeElement($searchEle);
$I->setValueTo($searchEle, 'kaiser');
$I->click('input[value="Google Search"]');
$I->getSentTo('https://www.google.com/search?q=kaiser');
?> 
```

Enter fullscreen mode Exit fullscreen mode

对于非常基本的场景，编写一个 Cept。

## Cests

如果您需要将您的步骤组合在一起以获得更好的可重用性，请编写一个 Cest:

```
<?php
// in file ExampleCest.php

class ExampleCest {
    public function _fetchStep(FunctionalTester $I, $url) {
        $I->startNewSession();
        $I->amOnPage($url);
    }

    public function checkForSearchBox(FunctionalTester $I) {
        $this->_fetchStep($I, 'http://www.google.com');
        $I->seeElement('input[title="Search"]');
    }

    public function doSearch(FunctionalTester $I) {
        $this->_fetchStep($I, 'http://www.google.com');
        $I->setValueTo('input[title="Search"]', 'kaiser shahid');
        $I->click('input[value="Google Search"]');
        $I->getSentTo('https://www.google.com/search?q=kaiser');
    }
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

Cests 允许您将多个相关的场景收集在一起，并允许重用公共步骤。注意，任何以`_`开头的函数都不被认为是测试，所以给任何不想自动执行的方法加上前缀。

Cest 类必须以`Cest`结尾，但是默认情况下它们可以是普通对象。

> 注意，Codecept 为 Cests 做依赖注入！`FunctionalTester $I`将使 Codecept 寻找`FunctionalTester`，如果找到，在将它传递给将要执行的方法之前初始化它。我们将在以后的文章中对此进行更深入的探讨。

## 单元测试

你可能已经习惯了:

```
<?php
// in ExampleTest

class ExampleTest extends \Codeception\Test\Unit {
    public function testScenario1() {
        $this->assertEquals(4, square(2), "unexpected result for square(2)");
    }
}
?> 
```

Enter fullscreen mode Exit fullscreen mode

注意，actor 不再是显式的——它的方法自动对类可用(可能通过`__call`)。

单元测试类必须以`Test`结尾并扩展`\Codeception\Test\Unit`。测试场景必须从`test`开始。

## 结论

每个测试套件都有一个参与者。参与者使用一个或多个助手来提供对该套件有用的测试作者断言和其他方法。测试可以是非常简单的场景(Cept)，复杂多变的场景(Cest)，或者传统的单元测试。