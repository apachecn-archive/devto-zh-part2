# 简单记忆

> 原文：<https://dev.to/alyson/simple-memorization-36ic>

我知道，但我必须知道更多这方面的特性。

*   简单简单

```
class Smile
  attr_accessor :smile

  def ensmile
    @smile = "SMILE"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   简单使用备忘录

```
class Smile
  attr_accessor :smile

  def ensmile
    @smile ||= "SMILE"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   开始测试 1)

```
class Smile
  attr_accessor :smile

  def ensmile
    @smile ||= begin
      "SMILE"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   开始测试 2)

```
class Smile
  attr_accessor :smile

  def ensmile(cry: false)
    @smile ||= begin
      cry ? "CRY" : "SMILE"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   测试包括处理错误

```
class Smile
  SmileError = Class.new(StandardError)

  attr_accessor :smile

  def ensmile(cry: false)
    @smile ||= begin
      raise SmileError if cry
      "SMILE"
    rescue SmileError
      "CRY"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

*   规格文件

```
require 'spec_helper'
require_relative '../lib/smile'

RSpec.describe Smile do
  let(:smile) { described_class.new }
  describe '#ensmile' do
    it 'returns "SMILE"' do
      expect(smile.smile).to be_nil
      expect(smile.ensmile).to eq 'SMILE'
      expect(smile.smile).to eq 'SMILE'
    end

    it 'returns "CRY"' do
      expect(smile.smile).to be_nil
      expect(smile.ensmile(cry: true)).to eq 'CRY'
      expect(smile.smile).to eq 'CRY'
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

回购:[https://github.com/shtakai/simple-smile](https://github.com/shtakai/simple-smile)