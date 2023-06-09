# 一种新的(对我来说)可枚举方法

> 原文：<https://dev.to/bethanyhaubert/a-new-to-me-enumerable-method-2iod>

有一天，我在一个视图上帮助我们的一个前端开发人员，他需要一组对象按照特定的顺序排列。问题是它需要一个非常难看而且有些混乱的查询:

```
@in_progress = migration_request_group.current_migration_requests.sort_by { |x|
 x.in_progress_in_group? ? 0 : 1 }.sort_by { |x| x.pending? ? 0 : 1 } 
```

它首先对`current_migration_requests`进行排序，如果当前循环的请求是`in_progress_in_group?`，那么它将被移动到数组中的第一个位置，否则它将被移动到第二个位置。然后*它检查该请求是否未决。如果是这样，它就被移到第一个位置。如果你只是第一次看代码，完全不容易理解。*

我知道一定有更好的方法。我只是不知道更好的方法是什么...

幸运的是，我在一个经验丰富的 Ruby 开发团队中，他们帮助了我。原来有一种叫做`partition`的方法实际上已经存在了一段时间。

```
pending, rest = migration_request_group.current_migration_requests.partition(&:pending?)
in_progress, rest = rest.partition(&:in_progress_in_group?)
@in_progress = pending + in_progress + rest 
```

下面是它的工作原理:
有两个数组:`pending`和`rest`。第一个，`pending`将存储块发现为真的任何对象，而`rest`将存储其余的对象。然后我做了另一个分区块，我把现有的`rest`数组分成两个数组:`in_progress`和`rest`。然后我按照要求的顺序组合数组。它可能会占用三行代码而不是一行，但我相信它会清晰得多。