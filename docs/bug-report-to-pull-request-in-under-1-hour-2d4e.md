# 错误报告，在 1 小时内提出请求

> 原文：<https://dev.to/tbrisker/bug-report-to-pull-request-in-under-1-hour-2d4e>

一个用户今天来到我们的 IRC 频道，抱怨搜索的一个问题:

```
[16:31] <user> Why is it that when I search parent_hostgroup = “somerandom” in
the “Hosts” searchbox, where “somerandom” doesn’t match anything, it gives me a 
list of all hosts instead of none? I have some issues with this behavior when 
performing searches using the API
[16:40] <tbrisker> user: sounds like a bug, can you open an issue on 
projects.theforeman.org?
[16:49] <user> tbrisker, https://projects.theforeman.org/issues/22556 
```

幸运的是，这是一个快速修复。但是，让我们一起深入了解一下，为什么它会失败。

我们使用一个名为 [Scoped Search](https://github.com/wvanbergen/scoped_search/) 的 gem 来处理应用程序中的搜索。它允许我们为用户提供使用强大的查询语言和自动补全功能在应用程序中搜索各种资源的能力。虽然在大多数情况下范围搜索是可行的，但有时某些查询需要更复杂的逻辑。通过向搜索定义传递一个:ext_method 参数，告诉范围搜索使用外部方法来生成查询，这是可能的。

parent_hostgroup 就是这样一个例子。Foreman 中的主机可以属于一个主机组，而该主机组又可以是一个或多个主机组的子组。要允许按父主机组(即属于某个主机组或其子主机组的任何主机)进行搜索，需要一个外部方法:

```
def search_by_hostgroup_and_descendants(key, operator, value)
  conditions = sanitize_sql_for_conditions(["hostgroups.title #{operator} ?", value_to_sql(operator, value)])
  # Only one hostgroup (first) is used to determined descendants. Future TODO - alert if result results more than one hostgroup
  hostgroup     = Hostgroup.unscoped.with_taxonomy_scope.where(conditions).first
  hostgroup_ids = hostgroup.subtree_ids
  if hostgroup_ids.any?
    opts = "hosts.hostgroup_id IN (#{hostgroup_ids.join(',')})"
  else
    opts = "hosts.id < 0"
  end
  {:conditions => opts}
end 
```

你能发现这个漏洞吗？

在 5 号线上。如果没有主机组与第 4 行的搜索匹配，`hostgroup`将是`nil`。这意味着`hostgroup.subtree_ids`应该抛出一个异常:

```
NoMethodError: undefined method `subtree_ids' for nil:NilClass 
```

这很容易解决，我们只需要在收集其子树 id 之前确保主机组存在:

```
def search_by_hostgroup_and_descendants(key, operator, value)
  conditions = sanitize_sql_for_conditions(["hostgroups.title #{operator} ?", value_to_sql(operator, value)])
  # Only one hostgroup (first) is used to determined descendants. Future TODO - alert if result results more than one hostgroup
  hostgroup     = Hostgroup.unscoped.with_taxonomy_scope.where(conditions).first
  if hostgroup.present?
    hostgroup_ids = hostgroup.subtree_ids
    opts = "hosts.hostgroup_id IN (#{hostgroup_ids.join(',')})"
  else
    opts = "1 < 0"
  end
  {:conditions => opts}
end 
```

果然，在最初的报告之后不到一个小时，我就准备好了一个 pull 请求，其中还包括一个单元测试，以确保这个函数在将来不会再次中断:

```
[17:23] <tbrisker> user: https://github.com/theforeman/foreman/pull/5249
[17:24] <user> tbrisker, thanks! :) 
```

34 分钟的工作和一个快乐的用户！

由于我试图遵循童子军规则，我还在第 9 行修复了一个小问题:这里的想法是返回一个总是被评估为假的条件，从而不返回任何搜索结果。然而，当 DB 看到类似 host.id < 0 的条件时，它会检查是否有任何 id 实际上小于 0。当你有成千上万的对象时，这个条件与其他条件(如默认范围、默认顺序、STI 类型等)相结合。)，即使针对索引执行，仍然需要一些时间:

```
foreman=# explain analyze SELECT “hosts”.* FROM “hosts” WHERE “hosts”.”type” IN (‘Host::Managed’) AND ((hosts.id < 0)) ORDER BY “hosts”.”name” ASC;
 QUERY PLAN 
— — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — 
 Sort (cost=62.19..62.20 rows=4 width=6134) (actual time=17.702..17.702 rows=0 loops=1)
 Sort Key: name
 Sort Method: quicksort Memory: 25kB
 -> Bitmap Heap Scan on hosts (cost=16.50..62.15 rows=4 width=6134) (actual time=17.692..17.692 rows=0 loops=1)
 Recheck Cond: ((type)::text = ‘Host::Managed’::text)
 Filter: (id < 0)
 Rows Removed by Filter: 43835
 Heap Blocks: exact=1429
 -> Bitmap Index Scan on index_hosts_on_type (cost=0.00..16.50 rows=12 width=0) (actual time=9.268..9.268 rows=43835 loops=1)
 Index Cond: ((type)::text = ‘Host::Managed’::text)
 Planning time: 0.359 ms
 Execution time: 17.808 ms
(12 rows) 
```

好的，那还不算太糟😄

但另一方面，当 RDBMS 看到类似 1 < 0 it immediately evaluates it to false and returns much faster (by about 3 orders of magnitude):
的条件时

```
foreman=# explain analyze SELECT "hosts".* FROM "hosts" WHERE "hosts"."type" IN ('Host::Managed') AND ((1 < 0)) ORDER BY "hosts"."name" ASC;
 QUERY PLAN 
------------------------------------------------------------------
 Sort (cost=0.01..0.02 rows=0 width=6134) (actual time=0.011..0.011 rows=0 loops=1)
 Sort Key: name
 Sort Method: quicksort Memory: 25kB
 -> Result (cost=0.00..0.00 rows=0 width=6134) (actual time=0.001..0.001 rows=0 loops=1)
 One-Time Filter: false
 Planning time: 0.241 ms
 Execution time: 0.077 ms
(7 rows) 
```

不是一个巨大的收获，但也没有伤害。也许学习这个技巧对其他情况可能会有用，在这些情况下，它可能会带来更大的收益。

但是等等，用户实际上并没有抱怨一个错误。他们抱怨说，当父主机组不存在时，会返回所有主机，而不是一个也不返回。他们为什么没有拿到`NoMethodError`？真奇怪！

好消息是 Scoped Search gem 是开源的，我可以看看里面，并试图找出发生了什么。

下面是在范围搜索中处理外部方法定义的代码:

```
def to_ext_method_sql(key, operator, value, &block)
  raise ScopedSearch::QueryNotSupported, "'#{definition.klass}' doesn't respond to '#{ext_method}'" unless definition.klass.respond_to?(ext_method)
  conditions = definition.klass.send(ext_method.to_sym,key, operator, value) rescue {}
  raise ScopedSearch::QueryNotSupported, "external method '#{ext_method}' should return hash" unless conditions.kind_of?(Hash)
  sql = ''
  conditions.map do |notification, content|
    case notification
      when :include then yield(:include, content)
      when :joins then yield(:joins, content)
      when :conditions then sql = content
      when :parameter then content.map{|c| yield(:parameter, c)}
    end
  end
  return sql
end 
```

第 3 行是在需要时实际调用外部方法的那一行。

但是那个`rescue {}`在那里做什么？这意味着任何发生在外部方法中的错误都会被无声无息地吞下，搜索将会像根本没有条件一样执行！这有效地抑制了外部方法内部的任何错误，这在 Ruby 和一般编程中被认为是[坏习惯](https://github.com/bbatsov/ruby-style-guide#dont-hide-exceptions)。这被认为是不好的做法有两个原因:

1.  遇到错误的用户(例如，通过发送无效的搜索查询)不会收到出错的提示。他们可能错误地认为他们收到的结果是正确的，这可能导致他们采取进一步的错误行动。在我们的示例中，假设用户想要创建一个脚本来删除某个主机组及其子组中的所有主机。如果他们键入错误的主机组名称，而不是收到一个错误或没有结果，他们可能会最终删除他们所有的主机！
2.  创建异常被抑制的方法的开发人员可能认为该方法运行正常，并且没有收到任何关于其中有任何问题的指示。在这种情况下，查看 git 历史，这个 bug 已经存在了 3.5 年而没有被发现！

因此，感谢开源的力量，我修复了范围搜索的问题，并向维护者发送了一个[拉请求](https://github.com/wvanbergen/scoped_search/pull/173):

```
def to_ext_method_sql(key, operator, value, &block)
  raise ScopedSearch::QueryNotSupported, "'#{definition.klass}' doesn't respond to '#{ext_method}'" unless definition.klass.respond_to?(ext_method)
  begin
    conditions = definition.klass.send(ext_method.to_sym, key, operator, value)
  rescue StandardError => e
    raise ScopedSearch::QueryNotSupported, "external method '#{ext_method}' failed with error: #{e}"
  end
  raise ScopedSearch::QueryNotSupported, "external method '#{ext_method}' should return hash" unless conditions.kind_of?(Hash)
  sql = ''
  conditions.map do |notification, content|
    case notification
      when :include then yield(:include, content)
      when :joins then yield(:joins, content)
      when :conditions then sql = content
      when :parameter then content.map{|c| yield(:parameter, c)}
    end
  end
  return sql
end 
```

现在，让我们只希望他们喜欢它，足以将其合并到项目中！😃

感谢您的阅读，我很乐意在下面的评论中或在 twitter 上听到反馈。