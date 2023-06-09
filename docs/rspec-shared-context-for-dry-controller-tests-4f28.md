# Rspec:干式控制器测试的共享上下文

> 原文：<https://dev.to/tomk32/rspec-shared-context-for-dry-controller-tests-4f28>

最好我先坦白:在我的规范中，共享上下文或共享实例的数量是一个很低的数字。不知道为什么我错过了这一趋势，但和 budget-fox.com 在一起，我保证会做得更好，并减少眼镜上的重复。

如果你的控制器规格和我的有点像，它们可能是这样的:

```
 # ...
  let(:user) { Fabricate(:user) }
  before do
    sign_in user, scope: :user
  end
  # ... 
```

这实际上很小，但是在几十个控制器上重复同样的模式，在 rspec-core 中有一些有用的东西:[共享
上下文](https://relishapp.com/rspec/rspec-core/docs/example-groups/shared-context)

如果你一遍又一遍地重复同样的`let`和`before`呼叫，最好将它们放入你的`spec/support`文件夹，在我的例子中是在`sign_in_context.rb`文件夹中。然后从控制器规范中包含上下文。

```
# only argument is:
# scope (defaults to :user)
RSpec.shared_context "sign in user", :shared_context => :metadata do |args|
  signed_in_scope = args.fetch(:model, :user)
  let(signed_in_scope) do
    Fabricate(signed_in_scope)
  end
  before :each do |c, a|
    sign_in self.send(signed_in_scope.to_sym), scope: signed_in_scope
  end
end 
```

并且在控制器规格:

```
RSpec.describe Admin::UsersController, type: :controller do
  include_context 'sign in user', model: :admin
  it "GET #index" do
    # ...
  end
end 
```