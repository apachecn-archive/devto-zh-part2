# 从 N 只企鹅中找出一只

> 原文：<https://dev.to/p810/finding-one-penguin-out-of-n-1o2l>

这个周末，我重温了我的一个爱好项目，我最近没有给予太多的关注。这是现已解散的 MMORPG 企鹅俱乐部的私人服务器。企鹅俱乐部是一个悠闲的游戏，玩家扮演一个彩色企鹅的化身，基本上是一起闲逛和聊天。他们可以玩迷你游戏来娱乐和赚取硬币，这使得他们可以为自己的虚拟角色购买衣服和冰屋家具，或者收养名为 Puffles 的虚拟宠物。它是在 2005 年由 Adobe Flash 构建的，之前它的前身*企鹅聊天*有过几次反复。

这不是我率先提出的想法，但我是它诞生的社区的一员；事实上，我就是这样开始编程的！对游戏的 Flash 客户端进行逆向工程，以及理解 PHP 中套接字和面向对象编程背后的概念，这些挑战对我青春期的大脑来说既艰难又有益。

今年早些时候，我开始重写我 13 岁时创建的游戏服务器。有了像 Composer 这样的新工具和 PHP 7 中的新语言特性，我想看看我在企鹅俱乐部的日子里已经走了多远。上周六，当我从 Bitbucket 中克隆我的存储库，并重新测试我已经完成的内容时，我想起了几个月前的一个独特问题。

[![Penguins](img/af5db24aafa9ea764e2e43ef6ac1097b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--22tf4M3_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vftodoxi2x36v77i7e5z.jpg)

## 问题:通过套接字找到客户

我有一个名为`getRequests()`的方法，它充当来自连接客户端的传入数据的 [`Generator`](http://php.net/manual/en/language.generators.overview.php) 。它将被服务器守护进程迭代，然后传递给一个回调函数，该回调函数解释数据并由*对数据做一些事情*。

在`getRequests()`中有一个对 [`socket_select()`](http://php.net/manual/en/function.socket-select.php) 的调用，以查看谁在试图与服务器对话。每个用户由一个`Client`对象表示，这个对象本质上是一个值对象，它存储关于用户及其套接字资源的元数据。当我们想要轮询客户机的输入数据时，包含每个连接的`Client`的列表被迭代，每个`Client`的套接字被放入一个临时列表。

当有数据被消费时，`socket_select()`将返回传递给它的数组变量中的适当套接字。*数组*被迭代，调用 [`socket_recv()`](http://php.net/manual/en/function.socket-recv.php) 。

如果`socket_recv()`返回`null`，那么我们知道用户已经断开连接，他们的对象需要从列表中删除。当我们用来识别用户的唯一数据是他们的套接字时，如何做到这一点呢？

这里可以使用一个`foreach`循环，调用`Client::getResource()`(它返回套接字)，并进行比较以找到正确的对象。但是我觉得这会使`getRequests()`方法变得复杂。

## 解:`SplObjectStorage`

我选择使用一个数组的子类 [`SplObjectStorage`](http://php.net/manual/en/class.splobjectstorage.php) 来存储`Client`对象。从语义上来说，这更有意义，因为我正在处理一组对象，这是一个专门用于这个目的的数据结构。`SplObjectStorage`提供一些有用的功能，但阵列的性能损失可以忽略不计。我可以将用于套接字标识的代码放在这里，我觉得这在语义上和可读性上都更合适。最后，键入安全！我现在可以输入提示`ClientList`，而不是简单地传递一个数组。

```
<?php

namespace p810\phpCP;

use Exception;

class ClientList extends \SplObjectStorage {
    public function findBySocket($socket): Client {
        foreach ($this as $key => $client) {
            if ($client->getResource() === $socket) {
                return $client;
            }
        }

        /** @todo make this better lol */
        throw new Exception;
    }
} 
```

我遇到的唯一障碍(如果我可以称之为障碍的话)是，起初我不确定如何在一个`Iterator`的项目中循环。一分钟的谷歌搜索给了我答案。

这是一个小小的成就，但却是我引以为豪的。这给了我一点动力，让我可以在整个周六下午继续做这个项目，而不会分心，也不会像往常一样开始做其他事情。

有时候，对于一个项目来说，最能带给我们满足感的是那些小小的挑战。

### 有用的外卖

*   PHP 的[标准 PHP 库](http://php.net/manual/en/book.spl.php) (SPL)包含了一堆有用的接口和类，用于设计像我上面实现的解决方案。值得去看看，去熟悉一下！
*   当感觉一个功能变得过于臃肿时，你应该考虑重构。和上面的`getRequests()`一样，某些功能更适合新对象。`ClientList`允许我将`getRequests()`的大小减少大约 12.5%。这不是一个很大的数字，但是对于将来可能会使用我的代码的人来说，这肯定会有所不同；它使功能更加人性化。