# 超越简单的记忆

> 原文：<https://dev.to/alyson/simple-memorization-beyond-17b7>

[本次回购](https://github.com/shtakai/simple-smile)

[来自上一篇](https://dev.to/alyson/simple-memorization-36ic)，
有问题。`memorization on falsy case.`

谢谢 [@jakebman](https://dev.to/jakebman) 。:)

我改了代码。

```
clas Smile
  SmileError = Class.new(StandardError)

  attr_accessor :smile

  # @smile is false on initialization.
  def initialize
    @smile = false
  end

  def ensmile(cry: false)
    @smile ||= begin
      puts 'memorize start'
      raise SmileError if cry
      puts 'memorize success'
      "SMILE"
    rescue SmileError
      puts 'memorize fail'
      "CRY"
    end
  end
end

(spec file is same) 
```

Enter fullscreen mode Exit fullscreen mode

又跑了`rspec`

```
FF

Failures:

 1) Smile#ensmile returns "SMILE"
 Failure/Error: expect(smile.smile).to be_nil

       expected: nil
            got: false #  ./spec/smile_spec.rb:8:in `block (3 levels) in <top (required)>'   2) Smile#ensmile returns "CRY"
 Failure/Error: expect(smile.smile).to be_nil

       expected: nil
            got: false #  ./spec/smile_spec.rb:14:in `block (3 levels) in <top (required)>'  Finished in 0.04088 seconds (files took 0.48848 seconds to load)
2 examples, 2 failures

Failed examples: rspec ./spec/smile_spec.rb:7 #  Smile#ensmile returns "SMILE"
rspec ./spec/smile_spec.rb:13 #  Smile#ensmile returns "CRY" 
```

Enter fullscreen mode Exit fullscreen mode

还好是因为初始化后的`false`。
只需修复规范文件。

```
-------- a/spec/smile_spec.rb
+++ b/spec/smile_spec.rb
@@ -5,13 +5,13 @@ RSpec.describe Smile do
   let(:smile) { described_class.new }
   describe '#ensmile' do
     it 'returns "SMILE"' do
-      expect(smile.smile).to be_nil
+      expect(smile.smile).to be_falsy
       expect(smile.ensmile).to eq 'SMILE'
       expect(smile.smile).to eq 'SMILE'
     end

     it 'returns "CRY"' do
-      expect(smile.smile).to be_nil
+      expect(smile.smile).to be_falsy
       expect(smile.ensmile(cry: true)).to eq 'CRY'
       expect(smile.smile).to eq 'CRY'
     end 
```

Enter fullscreen mode Exit fullscreen mode

然后重新跑了`rspec`。

```
Finished in 0.00841 seconds (files took 0.4119 seconds to load)
2 examples, 0 failures 
```

Enter fullscreen mode Exit fullscreen mode

好吧。去看看撬不撬。

```
1) nil
2.4.0 (main):0 > a=nil
=> nil
2.4.0 (main):0 > a ||= 'cat'
=> "cat"
2.4.0 (main):0 > a
=> "cat"

b) false
2.4.0 (main):0 > b=false
=> false
2.4.0 (main):0 > b ||= 'dog'
=> "dog"
2.4.0 (main):0 > b
=> "dog"

3) already set to Object something
2.4.0 (main):0 > r='roo tiger'
=> "roo tiger"
2.4.0 (main):0 > r ||= 'nwo'
=> "roo tiger"
2.4.0 (main):0 > r
=> "roo tiger" 
```

Enter fullscreen mode Exit fullscreen mode

如果我想让`false`保持初始化，它不起作用....😢

蒂尔:不要在虚假的情况下做，也不要在家里做。