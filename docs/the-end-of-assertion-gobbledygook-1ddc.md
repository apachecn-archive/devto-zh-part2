# 断言天书的终结

> 原文：<https://dev.to/burdettelamar/the-end-of-assertion-gobbledygook-1ddc>

我正在开发一个新的 Ruby gem `minitest_lucid`，它改进了来自`minitest`断言的错误输出。

要测试 Ruby 哈希:

*   **不太好**:默认`assert_equal`行为会产生混乱的结果。
*   **更好的**:同样的断言，但是增加了对`make_my_diffs_pretty!`(实际上是`minitest`本身的一部分)的调用，产生了更好的结果。
*   **最佳**:同样的断言，但是增加了`require 'minitest_lucid'`(即将推出的宝石)，产生最佳结果。

### 不太好

下面是从两个散列的失败的`assert_equal`中得到的结果，每个散列有八个条目:

```
 1) Failure:
MinitestLucidTest#test_hash [C:/Users/Burdette/Documents/GitHub/minitest_lucid/test/minitest_lucid_test.rb:28]:
-------- expected
+++ actual
@@ -1 +1 @@
-{:tauro=>"Cia ina do ip ocat doat.", :loquens=>"Dua sarat rad noad maat caea.", :lor=>"Eser in dolo eaata labor ut.", :dolo=>"Ipaat paal doat iruat ala magabor.", :offab=>"Ut dolore ua consal vaba caea.", :moam=>"Sunt sed te coma teu alaaame."}
+{:laboru=>"Laboab vaga dat maaua in venima.", :dolo=>"Ipaat paal doat iruat ala magabor.", :loquens=>"dua sarat rad noad maat caea.", :lor=>"Eser in dolo eaata labor ut.", :tauro=>"cia ina do ip ocat doat.", :amcae=>"Utatu cilaa cit siat commag seqa."} 
```

Enter fullscreen mode Exit fullscreen mode

### 较好的

如果您将`make_my_diffs_pretty!`添加到您的测试中，您会得到以下结果:

```
 1) Failure:
MinitestLucidTest#test_hash [C:/Users/Burdette/Documents/GitHub/minitest_lucid/test/minitest_lucid_test.rb:28]:
-------- expected
+++ actual
@@ -1,6 +1,6 @@
-{:tauro=>"Cia ina do ip ocat doat.",
- :loquens=>"Dua sarat rad noad maat caea.",
- :lor=>"Eser in dolo eaata labor ut.",
+{:laboru=>"Laboab vaga dat maaua in venima.",
  :dolo=>"Ipaat paal doat iruat ala magabor.",
- :offab=>"Ut dolore ua consal vaba caea.",
- :moam=>"Sunt sed te coma teu alaaame."}
+ :loquens=>"dua sarat rad noad maat caea.",
+ :lor=>"Eser in dolo eaata labor ut.",
+ :tauro=>"cia ina do ip ocat doat.",
+ :amcae=>"Utatu cilaa cit siat commag seqa."} 
```

Enter fullscreen mode Exit fullscreen mode

### 最好的

如果你把`require minitest_lucid`加到你的测试中，这是你从`minitest_lucid`得到的结果:

```
 1) Error:
MinitestLucidTest#test_hash:
Exception:
elucidation = {
    :missing_pairs => {
      :offab => 'Ut dolore ua consal vaba caea.',
      :moam => 'Sunt sed te coma teu alaaame.',
    },
    :unexpected_pairs => {
      :laboru => 'Laboab vaga dat maaua in venima.',
      :amcae => 'Utatu cilaa cit siat commag seqa.',
    },
    :changed_values => {
      :tauro => {
        :expected => 'Cia ina do ip ocat doat.',
        :got      => 'cia ina do ip ocat doat.',
      },
      :loquens => {
        :expected => 'Dua sarat rad noad maat caea.',
        :got      => 'dua sarat rad noad maat caea.',
      },
    },
    :ok_pairs => {
      :lor => 'Eser in dolo eaata labor ut.',
      :dolo => 'Ipaat paal doat iruat ala magabor.',
    },
}
    C:/Users/Burdette/Documents/GitHub/minitest_lucid/test/minitest_lucid_test.rb:28:in `test_hash' 
```

Enter fullscreen mode Exit fullscreen mode

### 供记录在案

下面是实际测试:

```
require 'minitest_lucid'

class MinitestLucidTest < Minitest::Test

  def test_hash
    expected = {
        :tauro => 'Cia ina do ip ocat doat.',
        :loquens => 'Dua sarat rad noad maat caea.',
        :lor => 'Eser in dolo eaata labor ut.',
        :dolo => 'Ipaat paal doat iruat ala magabor.',
        :offab => 'Ut dolore ua consal vaba caea.',
        :moam => 'Sunt sed te coma teu alaaame.',
    }
    actual = {
        :laboru => 'Laboab vaga dat maaua in venima.',
        :dolo => 'Ipaat paal doat iruat ala magabor.',
        :loquens => 'dua sarat rad noad maat caea.',
        :lor => 'Eser in dolo eaata labor ut.',
        :tauro => 'cia ina do ip ocat doat.',
        :amcae => 'Utatu cilaa cit siat commag seqa.',
    }
    assert_equal(expected, actual)
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

### 履行

测试代码需要的唯一的**变化是`require 'minitest_lucid'`(当然，在 gem 发布后安装)。**

### 范围

我希望新的 gem 能够处理 Ruby 核心和标准库中的所有集合:

*   `Hash`
*   `Array`
*   `Set`
*   `Struct`
*   `Openstruct`

### 问题

请随意发表意见:

*   宝石有没有更好的名字？我肯定希望名字中包含单词*minitest*；它*可以*包含单词*断言*，但是它需要第三个单词来表示宝石的功能。

*   我在考虑对数组使用 gem `diff-lcs`,因为它可以识别相似但错位的序列。是否应该有一个直接逐槽比较的选项？

*   有没有其他应该考虑的收藏？

*   是否有标量(非集合)的消息值得改进？