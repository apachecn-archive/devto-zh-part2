# Ranked_Model:多数据集的 Rails 瑰宝

> 原文：<https://dev.to/dudleysr/rankedmodel-a-rails-gem-for-multiple-data-sets-20bd>

[![Ranked_Model](../Images/23d4440cdc2784e9bb624cf8b39359e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y6xQKi65--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/edb6od4t39bjrzraj5fa.png) 
最近，我们在一个客户端 Rails 应用程序中遇到了一个问题，我们希望定制某些项目在不同页面上的显示顺序。管理员用户可以登录到使用 [`active_admin`](https://github.com/activeadmin/activeadmin) 构建的 CMS，创建一个包含公司信息的小部件，然后在创建或编辑小部件时手动设置列表中的位置，或者使用索引页面上的“上下移动”功能。

最初，我们利用 [`acts_as_list`](https://github.com/swanandp/acts_as_list) gem 来实现这个特性，因为它允许我们快速、轻松地添加我们需要的所有功能。

但是，当我们的客户要求每个小部件根据用户访问的页面有两个单独的排名时，我遇到了一个问题。如果用户在 Foo 产品页面上，小部件可能出现在位置 2，但是在 Bar 页面上，小部件将呈现在位置 3。

我最初的直觉是在小部件模型上创建一个新的`position`属性，并将新属性和现有的位置列传递给`acts_as_list`方法。然而，我遇到了一个障碍，因为`acts_as_list`不能在一个模型上处理两种不同的属性。

这是一个时间敏感的请求，当我深入到使用关联和连接表来添加这个特性的可能解决方案时，我不禁觉得应该有一个更快的解决方案，可以处理一个模型有两个不同的排名系统。

我的搜索让我发现了 [`ranked_model`](https://github.com/mixonic/ranked-model) ，这是一个与`acts_as_list`目标相似的宝石，但具有不同的实现和功能，其中大多数比`acts_as_list`方法更容易满足我们的标准。

安装完 gem 并将 RankedModel 包含在类文件中之后，您可以简单地用下面几行声明哪些属性将由`ranked_model`处理:

`ranks :foo`
T1】

这对于一个绿地项目或者一个新的属性来说效果很好，但是我把它添加到了一个已经应用了面向位置排名的属性中。两种 gem 都使用整数作为它们的数据类型，但是在大多数情况下，相似之处也就到此为止了。

`acts_as_list`在指定的属性中使用连续的数字，随着顺序的每次改变，根据需要增加和减少多个对象。例如，获取位置为 A: 1、B: 2、C: 3、& D: 4 的窗口小部件列表，然后将 D 移动到第一个位置，D 将变为 1，但是 B、C 和 A 都将递增，以完全重新排序列表。每次都有更多的方法可以运行，但是只需看一眼任何给定的对象，就能明白它的确切位置。

另一方面，使用相对较大的数字，包括正数和负数，来更快、更有效地对对象进行排序。用 ranked_model 创建同样的 4 个对象看起来有很大的不同，即 acts_as_list，更像这样:

`A: -2342567, B: -1437689, C: 1289078, D: 2165876`

由于 ranked_model 通常处理较大的整数，这可能会给开发和调试带来更多困难，尤其是在控制台中。一旦达到相当大数量的对象，仅从其位置属性来寻找排名中的相对位置将需要对细节的显著关注。然而，调用一个方法来实际移动这些位置是足够简单的。

为了模仿与 acts_as_list 示例相同的小部件重新定位，将 D 移动到第一个位置，代码需要调用:

```
D.update_attribute :foo_ranking_position, :first 
```

这将导致类似于:

`D: -2465876, A: -2342567, B: -1437689, C: 1289078`

所以你可以看到，使用这些大整数允许我们将 D 重新排序到前面，并且只修改一个对象！这在大型数据集的情况下变得特别有价值。如前所述，ranked_model 还允许我们调用多个属性上的排名，因此使用:

```
D.update_attributes {
  foo_ranking_position: :first,
  bar_ranking_position: :last
} 
```

完全可以接受。

不过，从 acts_as_list 迁移到 ranked_model 确实需要一些工作。由于我们已经从 acts_as_list 中获得了一个有序属性，试图直接写入并开始在其上使用 ranked_model 会导致一些奇怪的交互和不正确的排序。我的解决方案是将以下内容添加到数据库迁移中:

```
reversible do |direction|
  direction.up do
    Widget.order(:foo_position).each do |widget|
      widget.update_attribute :foo_position, :last
    end
    Widget.rank(:foo_position).each do |widget|
      widget.update_attribute :bar_position, :last
    end
  end
end 
```

这可能有点矫枉过正，几乎可以肯定可以重构，看起来更优雅或更有效(也可能应该是一个耙子任务)，但它满足了我们快速周转的需要。它实际上是从`acts_as_list`开始检查我们的原始顺序，并告诉`ranked_model`将其排列在列表的末尾，确保所有位置现在都有一个 ranked_model 兼容的位置。

在使用了 gem 特有的语法之后，为了修改在类文件中更新位置的方法和控制器，ranked_model 被证明是一种对多个 Rails 数据集进行排序和分级的有效方法。