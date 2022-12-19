# 排队还是不排队？

> 原文：<https://dev.to/hector6872/to-queue-or-not-to-queue-4d2a>

## 这不应该是个问题

[![](../Images/6348de383ad6ba1b1d97c19842a1ea04.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--2WTs7fZV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/3-queue/art/1.jpeg)  [排队等着看星球大战:1977–2000](https://flashbak.com/waiting-in-line-to-see-star-wars-1977-2000-26505/)

**没有人喜欢排队。大多数人讨厌浪费时间排队。我们成年生活中平均有 6 个月的时间[在等待我们的](https://www.mirror.co.uk/news/uk-news/fascinating-facts-about-how-we-spend-the-days-410973)[转变](http://www.dailymail.co.uk/travel/travel_news/article-3269123/How-time-spending-line-Infographic-reveals-hours-wasted-queuing-world-s-popular-tourist-attractions-s-Rome-patience-needed.html)——几乎每年 3 天。有关于队列的[书](https://www.amazon.com/Does-Other-Line-Always-Faster/dp/0761181229)。[纪录片](https://www.imdb.com/title/tt7663984/)。甚至[学术专家](https://www.youtube.com/watch?v=_CBD2z51u5c)也在排队。但是，当我们在编码时，我们经常忘记使用`queues`，结果却用`lists`来代替。**

> 当你使用正确的工具时，一切都会变得更好。

#### FIFO 还是 LIFO1！！！

超市里的一个[队列](https://www.youtube.com/watch?v=IPxBKxU8GIQ)就是 **FIFO。**我们通常[叠箱](https://media.giphy.com/media/8RH4MrJPTnVRK/giphy.gif)的方式是**后进先出。** **FIFO** 代表*“先进先出”。* **LIFO** 是*“后进先出”的缩写。*就这样。

[![](../Images/d605e03360276b2b96394c8e9fb49b6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MkIZn3el--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/3-queue/art/2.png) 
我用 [draw.io](https://www.draw.io/) 画了这张图。厉害的工具！

> 在实现 **FIFO** 队列时，我们通常使用`add`、`peek`(返回但不移除队列头)和`poll`(返回并移除队列头)方法，而我们在* * LIFO * *堆栈中分别将它们称为`push`、`peek`和`pop`。

#### 堆栈、队列和队列

在`stack`中，我们只能从一端添加和移除元素( **LIFO** )，在`queue`中，我们向一端添加元素，从另一端移除元素( **FIFO** )，连接这两个世界的是`deque`(又名*“双端队列”*)，我们可以从两端添加和移除元素。它们都不允许随机访问元素。