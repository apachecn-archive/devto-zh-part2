# rydux——ruby 应用程序的 Redux 存储

> 原文：<https://dev.to/alexdovzhanyn/rydux---a-redux-store-for-your-ruby-application-16cn>

大家好，我只是想写这篇文章让你们知道我创造的一个小的红宝石，Rydux。这个 gem 允许您在应用程序中将状态从逻辑中分离出来，并实现了一个更具功能性的 ruby 编程范例。你可以点击查看[。](https://www.github.com/alexdovzhanyn/rydux)

要使用 gem，需要它在你的应用程序的*根*的某个地方(或者在你需要它的任何地方都可以访问商店的某个地方)。接下来，在你的应用程序中创建一些 reducers，你可以把它们放在任何地方，但是推荐使用一个`reducers/`目录。一个示例缩减器看起来像这样:

```
 # reducers/user_reducer.rb

  class UserReducer < Rydux::Reducer

    # Your reducer MUST have a map_state function in order to do anything.
    def self.map_state(action, state = {})
      case action[:type]
      when 'SOME_RANDOM_ACTION' # You can add as many actions here as you'd like
        state.merge(some_random_data: true)
      when 'APPEND_PAYLOAD'
        state.merge(action[:payload])
      else
        state
      end
    end

  end 
```

Enter fullscreen mode Exit fullscreen mode

在你的应用程序中创建一个易于访问的商店:

```
 require 'reducers/user_reducer'

    # The key passed into .new here is the key at which this value
    # will be stored in the state. E.g. { user: whatever_user_state }
    Store = Rydux::Store.new(user: UserReducer) 
```

Enter fullscreen mode Exit fullscreen mode

有东西订阅商店:

```
 class MyClass
      def initialize
        Store.subscribe(self)
      end

      # Every instance that subscribes to the store will
      # get this state_changed method called whenever the state
      # in the store changes. Do whatever you want with your state here.
      def state_changed(state)
        # ...
      end
    end 
```

Enter fullscreen mode Exit fullscreen mode

要用新数据更新商店，您可以执行`dispatch`操作，比如:

```
 Store.dispatch(type: 'SOME_RANDOM_ACTION') 
```

Enter fullscreen mode Exit fullscreen mode

### 综合起来看:

```
require 'rydux'

class UserReducer < Rydux::Reducer
  @@initial_state = { name: 'Alex', age: 20 }

  def self.map_state(action, state = @@initial_state)
    case action[:type]
    when 'CHANGE_USER_NAME'
      state.merge(name: action[:payload][:name])
    else
      state
    end
  end

end

Store = Rydux::Store.new(user: UserReducer)

class Friend
  def initialize
    Store.subscribe(self)
    @users_name = Store.state.user.name
  end

  def state_changed(state)
    @users_name = state.user.name
  end

  def greet_user
    puts "Hello, #{@users_name}"
  end
end

# Create a new friend (this will subscribe it to the store)
friend = Friend.new
friend.greet_user #=> Hello, Alex

# Change a value in the store
Store.dispatch(type: 'CHANGE_USER_NAME', payload: { name: 'Mike' })
friend.greet_user #=> Hello, Mike 
```

Enter fullscreen mode Exit fullscreen mode