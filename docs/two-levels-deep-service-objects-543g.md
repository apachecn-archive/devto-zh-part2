# 两级深层服务对象

> 原文：<https://dev.to/bpohoriletz/two-levels-deep-service-objects-543g>

*   时间:20-30 分钟
*   级别:初级/中级
*   代码: [GitHub](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/two_level_deep_service_objects)

在我开始写这篇文章之前，我搜索了其他从这个角度看代码的人——发现了一个[你的代码有多深？](http://edmundkirwan.com/general/tuples.html)

我需要一个复杂服务对象的例子，并在一个对话源[文件](https://github.com/discourse/discourse/blob/482c615ef882c1953070125e0a813683f979e5ff/app/services/user_updater.rb)中找到了它。我并不是在批评这段代码，这是一个很好的例子，说明了我打算在同一个[库](https://github.com/discourse/discourse/blob/482c615ef882c1953070125e0a813683f979e5ff/app/services/user_merger.rb)中实现什么

我个人更喜欢小类、短的、单一目的的方法，而不是一大段代码。话虽如此，我不得不承认这种方法有一个显著的缺点——方法通常很短，因为它们在内部调用其他方法，这些方法调用其他方法，这些方法调用其他方法……所以在弄清楚方法实际上做了什么之前，你必须进入兔子洞，有时你会同时失去大局。

我不想这样做，我不想查看 5-10 个私有方法来弄清楚调用公共方法的含义，我希望公共方法能够在我第一次看到它的时候马上告诉我这个故事——也许有一种方法？

有一次，我在 Sandi Metz 的演讲中看到了她描述的一个复杂性度量标准:斜视测试。虽然这个标准被用来衡量嵌套条件的复杂性，但是我们可以考虑以类似的方式调用方法。如果一个方法调用另一个方法，让我们缩进它，就像它是一个嵌套的 If:

```
def parent
  child_level_two
  # some code
end

def child_level_two
  # some code
  child_level_three
end

def child_level_three
  # some code
  child_level_four
  # some code
end 
```

现在让我们写下所有涉及的方法，并缩进每个嵌套调用

```
def parent
  child_level_two
    child_level_three
  child_level_two
    child_level_three
    child_level_three
  child_level_two
  child_level_two
    child_level_three
    child_level_three
      child_level_four
  child_level_two
  child_level_two
    child_level_three
      child_level_four
      child_level_four
end 
```

我们得到了形状变化的相似图形，形状变化越大，嵌套越多。

# 第一步

这一步的代码修改可以在[对应的提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/a8bf6246520d6ee45db7b1aa708056fa1821de25)中找到

在重构之前，我将外部依赖项提取到了`dependencies.rb`中，并为所需的方法添加了占位符

```
# user_updater.rb
require 'active_support'
require 'active_support/core_ext/object/blank'
require 'active_support/core_ext/time/calculations'
require 'active_model'

require_relative 'dependencies'

class UserUpdater
  delegate :change_post_owner, to: :guardian

  def update(attributes = {})
    save_options = false
    saved = nil
    old_user_name = user.name.present? ? user.name : ""

    user_profile = user.user_profile
    user_profile.location = attributes.fetch(:location) { user_profile.location }
    user_profile.dismissed_banner_key = attributes[:dismissed_banner_key] if attributes[:dismissed_banner_key].present?
    user_profile.website = format_url(attributes.fetch(:website) { user_profile.website })
    user_profile.profile_background = attributes.fetch(:profile_background) { user_profile.profile_background }
    user_profile.card_background = attributes.fetch(:card_background) { user_profile.card_background }
    if SiteSetting.enable_sso && !SiteSetting.sso_overrides_bio
      user_profile.bio_raw = attributes.fetch(:bio_raw) { user_profile.bio_raw }
    end

    user.name = attributes.fetch(:name) { user.name }
    user.locale = attributes.fetch(:locale) { user.locale }
    user.date_of_birth = attributes.fetch(:date_of_birth) { user.date_of_birth }
    if can_grant_title?(user)
      user.title = attributes.fetch(:title) { user.title }
    end

    # special handling for theme_key cause we need to bump a sequence number
    if attributes.key?(:theme_key) && user.user_option.theme_key != attributes[:theme_key]
      user.user_option.theme_key_seq += 1
    end

    OPTION_ATTR.each do |attribute|
      if attributes.key?(attribute)
        save_options = true

        if [true, false].include?(user.user_option.send(attribute))
          val = attributes[attribute].to_s == 'true'
          user.user_option.send("#{attribute}=", val)
        else
          user.user_option.send("#{attribute}=", attributes[attribute])
        end
      end
    end

    # automatically disable digests when mailing_list_mode is enabled
    user.user_option.email_digests = false if user.user_option.mailing_list_mode

    fields = attributes[:custom_fields]
    if fields.present?
      user.custom_fields = user.custom_fields.merge(fields)
    end

    User.transaction do
      if user.user_option.save && user_profile.save && user.save
        StaffActionLogger.new(@actor).log_name_change(
          user.id,
          old_user_name,
          attributes.fetch(:name) { '' }
        )

        saved = true
      end
    end

    saved
  end

  private

  attr_reader :user, :guardian

  def initialize(actor, user, guardian = Guardian.new(actor))
    @user = user
    @guardian = guardian
    @actor = actor
  end

  def format_url(website)
    return nil if website.blank?
    website =~ /^http/ ? website : "http://#{website}"
  end
end 
```

# 第二步

这一步被分成几个小块:

*   #2.1 重构`UserUpdater#update`方法([提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/60a6e89f7be50eb4b143d6aefa0357e0899e04ae)
*   #2.2 重构 2.1 中提取的方法
    *   #2.2.1 重构`UserUpdater#update_user_profile`方法([提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/4c383dd20b20addc34b20e6cfa7dc953aac5ad90))
    *   #2.2.2 重构`UserUpdater#update_user`方法([提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/d362cdad5cd45c2319fcba961ef92ea85673589f)
    *   #2.2.3 重构`UserUpdater#update_user_option`方法([提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/618215dd0d8183c76ebf7549fa8eb9771523bb19))
    *   #2.2.4 重构`UserUpdater#save_user_data`方法([提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/3f3fb36a5d502bcb570f4aa150faf2bcee073bba))

我将在这里跳过细节，因为这些是非常直接的提取，它们是为了简化`UserUpdater#update`方法，使它变得简单易懂。

```
# user_updater.rb
class UserUpdater
  delegate :change_post_owner, to: :guardian

  def update(attributes = {})
    old_user_name = user.name.present? ? user.name : ""

    update_user_profile( user.user_profile, attributes )
    update_user( user, attributes )
    update_user_option( user.user_option, attributes )
    save_user_data(user, old_user_name, attributes)
  end

  private
  # ... 
```

然而，我认为太多的信息被隐藏了。从方法定义中，我们不再看到:

1.  有一笔交易
2.  某些部分只有在满足特定条件时才会更新
3.  我们使用常量来决定更新什么
4.  我们根据事务的结果返回(并依赖于某处的返回值)`true` / `false`并不明显

除了隐藏信息之外，我非常不喜欢新的`UserUpdater#update_user_profile`方法——它是一个私有方法，只包含其他私有方法，同样，里面没有条件

```
# user_updater.rb
def update_user_profile( user_profile, attributes )
  update_geo_data( user_profile, attributes )
  update_web_data( user_profile, attributes )
  update_background_data( user_profile, attributes )
  update_bio_raw( user_profile, attributes )
end 
```

我相信像这样的方法只会带来额外的复杂性，没有存在的权利

# 第三步

这一步的代码修改可以在[对应的提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/252e123e81951e4a72c7d08dbcbedfbba032a71c)中找到

在这一步中，我们删除了(几乎)只是包装其他私有方法的私有方法，并更改了剩余的方法名，以更好地反映意图

```
# user_updater.rb
class UserUpdater
  delegate :change_post_owner, to: :guardian
  delegate :log_user_name_change, to: :StaffActionLogger

  def update(attributes = {})
    old_user_name = user.name.present? ? user.name : ""
    user_profile = user.user_profile
    user_option = user.user_option

    set_user_profile_geo_data( user_profile, attributes )
    set_user_profile_web_data( user_profile, attributes )
    set_user_profile_background_data( user_profile, attributes )
    set_user_profile_bio_raw( user_profile, attributes )

    set_user_bio(user, attributes, update_title: can_grant_title?(user) )
    user.custom_fields = user.custom_fields.merge( attributes.fetch( :custom_fields, {} ) )

    set_user_option_theme_key(user_option, attributes)
    OPTION_ATTR.each { |attribute| set_user_option_single_attribute(user_option, attributes, attribute) }
    # automatically disable digests when mailing_list_mode is enabled
    user_option.email_digests = false if user_option.mailing_list_mode

    return false unless User.transaction { user.user_option.save && user.user_profile.save && user.save }
    log_user_name_change( user.id, old_user_name, attributes.fetch(:name) { '' } )

    return true
  end 
```

与之前的版本相比,`UserUpdater#update`几乎变大了三倍，但是它现在也告诉我们一个更好的故事。

它并不完美(也永远不会完美),但我们会再做一步来改进它，并在之后与第二步中的较短版本进行比较。

# 第四步

这一步的代码修改可以在[对应的提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/56690ffe09c4d832feb346456ca00826d8517bd6)中找到

在这一步，我们将做最后的抛光和比较结果

```
# user_updater.rb

# Short version
class UserUpdater
  # ...
  def update(attributes = {})
    old_user_name = user.name.present? ? user.name : ""

    update_user_profile( user.user_profile, attributes )
    update_user( user, attributes )
    update_user_option( user.user_option, attributes )
    save_user_data(user, old_user_name, attributes)
  end
# Two levels deep version
class UserUpdater
  # ...
  def update(attributes = {})
    @attributes = attributes
    old_user_name = user.name.present? ? user.name : ""

    set_user_profile_geo_data
    set_user_profile_web_data
    set_user_profile_background_data
    set_user_profile_bio_raw if should_update_user_profile_bio_raw?

    set_user_bio( update_title: can_grant_title?(user) )
    user.custom_fields = user.custom_fields.merge( attributes.fetch( :custom_fields, {} ) )

    set_user_option_theme_key if should_update_user_option_theme_key?
    OPTION_ATTR.each do |attribute|
      set_user_option_single_attribute( attribute )
    end
    # automatically disable digests when mailing_list_mode is enabled
    user_option.email_digests = false if user_option.mailing_list_mode

    return false unless User.transaction { save_user_and_related_entities }
    log_user_name_change( user.id, old_user_name, attributes.fetch(:name) { '' } )

    return true
  end 
```

我相信较长的版本有以下好处:

1.  代码通过大量的实现细节讲述了一个伟大的故事
2.  它遵循开放/封闭原则
3.  离完整的私有方法定义最多只有一步之遥

# 总结

为了达到两级深度，代码必须遵循以下规则:

1.  公共方法不能调用同一类的其他公共方法
2.  私有/受保护的方法不能调用其他私有/受保护的方法

我还有两条经验法则

1.  避免私有方法中的条件
2.  条件必须反映私有方法，而不是布尔表达式

代码:

*   [储存库](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/two_level_deep_service_objects)
*   [第一步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/a8bf6246520d6ee45db7b1aa708056fa1821de25)
*   [步骤#2.1](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/60a6e89f7be50eb4b143d6aefa0357e0899e04ae)
*   [步骤#2.2.1](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/4c383dd20b20addc34b20e6cfa7dc953aac5ad90)
*   [步骤#2.2.2](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/d362cdad5cd45c2319fcba961ef92ea85673589f)
*   [步骤 2.2.3](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/618215dd0d8183c76ebf7549fa8eb9771523bb19)
*   [步骤#2.2.4](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/3f3fb36a5d502bcb570f4aa150faf2bcee073bba)
*   [第三步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/252e123e81951e4a72c7d08dbcbedfbba032a71c)
*   [第四步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/56690ffe09c4d832feb346456ca00826d8517bd6)

# 引人深思

1.  这些规则在服务对象中很容易遵循，因为通常它们描述了一些过程/算法——它们还可以应用到哪里？
2.  委托方法怎么样，我们可以在测量的时候威胁它们是私有的吗？
3.  访问器方法怎么样，我们可以在测量的时候威胁它们是私有的吗？