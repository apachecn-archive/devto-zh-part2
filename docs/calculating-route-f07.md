# 正在计算路线…

> 原文：<https://dev.to/runtime-revolution/calculating-route-f07>

<figure>[![](img/55eefd3c34c41e9a9f4a0c6f7dfc0b5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jY0NeDru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHP_UqWnjhETCy1jijaoE4A.jpeg) 

<figcaption>照片由[尼基塔·卡恰诺夫斯基](https://unsplash.com/photos/L7CwfCSmpfM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

Ruby on Rails 的路由机制是我花了最长时间才理解的主题。作为应用程序和世界之间的门户，理解它的功能、可能性和限制对于预测用户体验是至关重要的。

任何 RoR 教程都会告诉你设置有多简单。*只需输入*

```
resources :product 
```

*"您将自动拥有所有这些路线:"*

```
Verb URI Pattern Controller#Action
GET /products products#index
GET /products/:id products#show
GET /products/new products#new
POST /products products#create
GET /products/:id/edit products#edit
PUT /products/:id products#update
PATCH /products/:id products#update
DELETE /products/:id products#destroy 
```

由于之前没有使用 RESTful APIs 的经验，我有点怀疑仅仅五个方法(GET、POST、PUT、PATCH、DELETE)是否足以提供我的应用程序(或者任何应用程序)所需的所有功能。如果我需要三种不同的方法来更新产品，会怎么样？如果我需要为没有模型的东西选择路线，该怎么办？

事实证明，掌握 routes.rb 文件并不那么容易。ActionController::RoutingError 被 [Rollbar](https://rollbar.com/blog/top-10-ruby-on-rails-errors/) 评为 1000 多个 ror 项目中最常见的错误。

Ruby on Rails Guides 关于 [Routing](http://guides.rubyonrails.org/routing.html) 的页面，虽然字数很少，但是有大量的例子，仍然是一个相当长的页面。然而，我怀疑还有更多可说的。

Vaidehi Joshi 有一个关于这个主题的[演示](https://youtu.be/lEC-QoZeBkM)，有 123 张幻灯片！剧透:这个话题更多的是关于路由机制是如何形成的。

开发 MVP 是一项快速而艰苦的工作。你跟随你的直觉，在需要知道的基础上行动——反思随之而来。现在，我有时间回顾一下 routes.rb 文件。我当时知道的有多少，现在知道的又有多少。现在对我来说很明显，我可以为“未建模的东西”创建一条路线。同样显而易见的是，没有控制器也可以逃脱！自己试试:

```
Rails.application.routes.draw do
 get 'not-modeled-something', to: proc { [200, {}, [“Hello, controllerless!”]] }
end 
```

为了进一步阅读，我最近发现了 RichOnRails.com 的这个教程，我认为它非常完整。快乐阅读和*一路顺风*！

*如今我在* [*工作运行时革命*](http://www.runtime-revolution.com/) *。在这里工作是一次很好的学习经历，我开始对 Rails 有所了解。*

* * *