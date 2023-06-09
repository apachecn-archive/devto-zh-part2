# 急切地加载@current_user 以节省关于 pundit 策略的时间

> 原文：<https://dev.to/nodefiend/eager-loading-currentuser-to-save-time-on-pundit-policies-40m7>

## 问题:

> 如果您的 rails 控制器能够运行批量创建/读取/更新/删除，那么您如何快速运行策略检查而不使您的服务器陷入瘫痪呢？

阿尔贝托·乔尔·菲利普拍摄的照片

*这篇文章不是关于安装的，如果你对安装感兴趣，点击[这里](https://github.com/varvet/pundit)T3】*

### 上下文:

*   我们的控制器是 Search/save/delete，如果您没有通过 search an `id`它会认为您正在运行一个`GET ALL`并返回一个数据库对象集合。
*   我们为每个数据库模型/控制器设置了权威策略。
*   每个策略也扩展了一个基本策略。
*   我们所有的控制器都扩展了一个基础 api 控制器。

假设我们想获得一个用户参与的项目列表:

`frontend_api_request.js`

```
 //pass it an empty object for a standard get all request to the controller.
const ApiRequestSearch = async (data) => {
    const response = await fetch('https://yourapi.com/projects',data);
    const json = await response.json();
}

ApiRequestSearch({data:{}); 
```

当它到达后端时，它会到达我们的基本控制器:

`base_controller.rb`

```
class Api::ApiController < ActionController::Base
    include Pundit
    before_action :set_current_user
    ...
end 
```

`base_controller_helper.rb`
我们的基本控制器使用一个助手来:

*   像这样设置当前认证用户:

```
def set_current_user
  User.current = current_user    
end 
```

*   实例化权威政策(如果你知道更好的方法，请告诉我)

```
def yourPolicy
  "#{controller_name.classify}Policy".constantize
end 
```

当我们的前端请求被接收时，首先它到达我们的基本搜索方法，在那里我们将数据和当前用户传递到我们的 pundit 策略中。

`base_controller.rb`

```
def search
...
    #If the policy returns false, we return a 403 to the front end.
    if ! self.yourPolicy.new(current_user, @collection).search?
        ApiErrors.raise_custom(message: 'You are not authorized to search!',     
        status: 403)
    end
...
end 
```

现在，我们点击基本应用程序策略，在这里我们初始化运行策略检查所需的所有内容，另外，如果我们只想运行一般的搜索策略检查，我们可以在这里添加它

`base_policy.rb`

```
class ApplicationPolicy
  attr_reader :user, :record, :collection

  def initialize(user, record)
    @user   = user
    @record = record
    @collection = record
  end 
```

因此，如果想让我们的请求超级滞后和低效，我们可以这样做:(是的，这是我的第一个想法-。-)

`projects_policy.rb`

```
class ProjectPolicy < ApplicationPolicy
# BAD ! DONT DO IT
    def search?
        @collection.map do |project|
            return true if project.user_id == @current_user.id 
            return false
        end
    end
end 
```

## 如何才能更好的做到这一点？

相反，让我们使用[急切加载](https://www.allerin.com/blog/eager-loading-inwith-rails)(提前)来提前获得我们需要的所有关联，这样我们就不必花时间检查数组中的每个对象。

让我们回到设置当前用户的地方，为当前用户添加一个类变量，并加载关联。

`base_controller_helper.rb`

```
def set_current_user
  User.current = current_user
  # eager load the associations we need 1
  # time, so we don't need to do it later
  @current_user = User.includes(:projects).find(current_user.id) if current_user
end 
```

设置这个会给我们一个用户模型，并加载关联。
这样，当我们点击`base_policy.rb`时，我们可以使用关联并检查它们，而不必进行额外的查询，或者映射并检查每个对象。

让我们回到我们的基本策略，并传递我们的新`@current_user`:

`base_controller.rb`

```
def search
...
    #If the policy returns false, we return a 403 to the front end.
    if ! self.yourPolicy.new(@current_user, @collection).search?
        ApiErrors.raise_custom(message: 'You are not authorized to search!',     
        status: 403)
    end
...
end 
```

现在，在我们的策略中，我们可以这样做:

`projects_policy.rb`

```
class ProjectPolicy < ApplicationPolicy
# GOOD ! DO IT
    def search?
        # get all IDS of the projects current user can access
        current_user_project_ids = @current_user.projects.pluck(:id)
        # get the ids of the collection that the user is searching
        collection_ids = @collection.pluck(:id)
        #compare both arrays, if empty , return true! 
        (collection_ids - current_user_project_ids).empty?
    end
end 
```

因此，像这样的数组比较，比进行额外的查询，或通过数组项列表进行映射要快得多，对吗？

如果你有任何改进的建议，或者我只是犯了愚蠢的错误，请在下面的评论中告诉我，我完全接受建设性的批评！

`thanks for reading !`