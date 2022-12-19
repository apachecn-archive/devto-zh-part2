# 解释:冒号和冒号:在 Ruby 中像我五岁一样

> 原文：<https://dev.to/aswathm78/explain-colon-and-colon-in-ruby-like-im-five-2646>

我正在学习 rails，完成了 ruby 基础。在 ruby 中仍然与冒号前后混淆。

```
 def create
    @user = User.new(user_params)

    respond_to do |format|
      if @user.save
        format.html { redirect_to @user, notice: 'User was successfully created.' }
        format.json { render :show, status: :created, location: @user }
      else
        format.html { render :new }
        format.json { render json: @user.errors, status: :unprocessable_entity }
      end
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

这是一个由 rails scaffold 命令自动生成的创建方法。

请解释代码是怎么回事。