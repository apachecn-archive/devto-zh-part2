# 触摸触发其他触摸

> 原文：<https://dev.to/mmyoji/touch-triggers-other-touches--4c09>

[active record::Persistence # touch](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-touch)是一个有用的方法，我喜欢。

但我并没有真正理解它的规范，这导致了我们的 Rails 项目陷入僵局。你必须关心它的影响。

```
class Post < ApplicationRecord
  belongs_to :post_category, touch: true
  belongs_to :writer, touch: true
end

# This class is a pseudo class
class SomeClass
  def initialize(post, args)
    @post = post
    # ...
  end

  def call
    # ...
    @post&.touch(:published_at)
    # triggers following:
    # @post.post_category.touch
    # @post.writer.touch
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在`SomeClass#call`中，`Post#touch`被调用，我以为这只更新了`post.published_at`，但也更新了`post_category.updated_at`和`writer.updated_at`。

上面的代码只是一个例子，当你在实际应用中随便使用`touch`时可能会有麻烦。

你只关心部署前的日志:)