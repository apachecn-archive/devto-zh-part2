# 新的东西

> 原文：<https://dev.to/burdettelamar/something-new-1abg>

我正在做一些新的东西:一个补充 Ruby gem `minitest`的日志程序。

其特点之一是分析某些失败的判决，即那些涉及集合。

这里有一个比较两个散列的小测试:

```
require 'minitest_log'

class Example < Minitest::Test

  def test_example
    MinitestLog.open do |log|
      expected = {:a => 0, :b => 1, :c => 2, :d => 3, :e => 4, :f => 5}
      actual = {:h => 5, :g => 4, :d => 1, :c => 0, :b => 1, :a => 0}
      log.verdict_assert_equal?(:analyzed, expected, actual)
    end
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

日志当然会显示失败的结论，以及预期值和实际值。

它还包含这个:

```
<analysis>
  <missing e='4' f='5'/>
  <unexpected h='5' g='4'/>
  <changed c='{:expected=&gt;2, :actual=&gt;0}' d='{:expected=&gt;3, :actual=&gt;1}'/>
  <ok a='0' b='1'/>
</analysis> 
```

Enter fullscreen mode Exit fullscreen mode

下面是日志:

```
<log>
  <summary verdicts='1' failures='1' errors='0'/>
  <verdict method='verdict_assert_equal?' outcome='failed' id='analyzed'>
    <exp_value a='0' b='1' c='2' d='3' e='4' f='5'/>
    <act_value h='5' g='4' d='1' c='0' b='1' a='0'/>
    <analysis>
      <missing e='4' f='5'/>
      <unexpected h='5' g='4'/>
      <changed c='{:expected=&gt;2, :actual=&gt;0}' d='{:expected=&gt;3, :actual=&gt;1}'/>
      <ok a='0' b='1'/>
    </analysis>
    <exception class='Minitest::Assertion'>
      <message>
        --- expected +++ actual @@ -1 +1 @@ -{:a=&gt;0, :b=&gt;1, :c=&gt;2,
        :d=&gt;3, :e=&gt;4, :f=&gt;5} +{:h=&gt;5, :g=&gt;4, :d=&gt;1, :c=&gt;0,
        :b=&gt;1, :a=&gt;0}
      </message>
      <backtrace>
        <![CDATA[
example.rb:23:in `block in test_example'
example.rb:6:in `test_example']]>
      </backtrace>
    </exception>
  </verdict>
</log> 
```

Enter fullscreen mode Exit fullscreen mode