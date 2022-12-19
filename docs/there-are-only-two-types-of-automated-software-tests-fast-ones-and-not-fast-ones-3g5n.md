# 自动化软件测试只有两种类型，快速的和非快速的

> 原文：<https://dev.to/geshan/there-are-only-two-types-of-automated-software-tests-fast-ones-and-not-fast-ones-3g5n>

测试检查代码是否做了预期的事情。它也给软件工程师信心，让他们相信代码是按预期工作的。这相当于软件中的错误更少或没有。您一定听说过许多类型的自动化软件测试。有单元测试、集成测试、功能测试、验收测试、冒烟测试等。根据 Guru99 的[帖子](http://www.guru99.com/types-of-software-testing.html)，有 100 多种软件测试。在这篇文章中，我将把自动化软件测试分为两类，快速的和非快速的。

[![](img/37fa19e2d65cedc2600762c746c96891.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1UhGe8MA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5570/1%2Aw101yq12wfyUevObORbC2w.jpeg)

## 如何区分慢速和快速测试？

一般来说，如果你的整个测试套件在几秒钟内运行，这是很快的。如果你的整个测试套件在几分钟/小时内运行，那就太慢了。为了让你的测试变小，你需要让你的应用程序变小。因为在您的持续集成(CI)服务上运行的更快的测试将会给您更快的反馈。也许是时候去微服务了？

让我们讨论更多关于快速和非快速(慢速)自动化软件测试的内容。

## 快速测试

快速测试是测试一个代码单元的代码，通常是一个方法。单元测试是一种快速测试。它们不依赖任何外部因素。外部依赖包括文件系统、数据库、web 服务器、网络或任何第三方 API 或服务。单元测试甚至模仿他们需要的其他代码元素，就像其他类及其方法一样。这使得测试集中在一个单元上，它们以毫秒/秒而不是分钟运行。下面是一个简单的例子:

```
<?php

namespace DataProvider\Example\Test;

use DataProvider\Example\Checkout;
use PHPUnit_Framework_TestCase;

/**
 * Checkout test for Cash and Credit card.
 */
class CheckoutTest extends PHPUnit_Framework_TestCase
{
    /**
     * @var Checkout
     */
    protected $checkout;

    public function setup()
    {
        $this->checkout = new Checkout();
    }

    /**
     * Data provider for testCalculateTotal
     * variables are in the order of
     * $paymentMethod, $expectedTotal.
     * 
     * @return type
     */
    public function paymentMethodProvider()
    {
        return [
            ['Cash', 100.00],
            ['Credit Card', 95.00],
        ];
    }

    /**
     * Test to check if the order total is calculated correctly
     * for given payment method.
     * 
     * @param string $paymentMethod
     * @param float  $expectedTotal
     * 
     * @dataProvider paymentMethodProvider
     */
    public function testCalculateTotal($paymentMethod, $expectedTotal)
    {
        $this->checkout->calculateTotal($paymentMethod);
        $this->assertEquals(
            $this->checkout->getTotal(),
            $expectedTotal,
            sprintf('Testing total calculation for %s.', $paymentMethod)
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里查看完整的代码，是的，这是一个简单的类，没有代码或外部依赖。

一些集成测试也可以是快速测试。这些集成测试可以测试许多类。它们不应该依赖上面提到的任何外部因素来获得速度。因此，这些测试仍将在几秒钟内运行，而不需要几分钟就能完成。

## 非快速测试(慢速测试)

任何需要更长时间运行的测试都不是快速测试(慢速测试)。通常，这些类型的测试需要加载整个应用程序来测试它。这些类型的测试依赖于外部依赖性。外部依赖包括文件系统、数据库、web 服务器、网络、第三方 API 或服务。

需要在浏览器上加载完整的 web 应用程序的验收测试是一种缓慢的测试。即使是冒烟测试，如果它需要加载整个应用程序并且需要很长时间来执行，也属于这一类。

```
@checkout
Feature: Checkout with offline payment
    In order to pay with cash or by external means
    As a Customer
    I want to be able to complete checkout process without paying

    Background:
        Given the store operates on a single channel in "France"
        And the store has a product "PHP T-Shirt" priced at "$19.99"
        And the store ships everywhere for free
        And the store allows paying offline

    @ui
    Scenario: Successfully placing an order
        Given I am logged in customer
        And I have product "PHP T-Shirt" in the cart
        When I proceed selecting "Offline" payment method
        And I confirm my order
        Then I should see the thank you page 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子来自于 [Sylius](https://github.com/Sylius/Sylius/blob/master/features/checkout/checkout_with_offline_payment.feature) 项目，测试在浏览器上使用离线支付方式结账。

## 结论

测试对于一个健壮的软件应用程序来说是非常重要的。自动化测试+ CI 是任何可靠的软件应用程序的四大支柱之一。祝您测试愉快，希望您的测试在几秒钟内完成，而不是几分钟。

* * *

*最初发表于[geshan.com.np](https://geshan.com.np/blog/2016/03/there-are-only-two-types-of-automated-software-tests/)。*