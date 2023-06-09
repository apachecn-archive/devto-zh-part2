# [UA] Service Objects Глибиною В Два Рівні

> 原文：<https://dev.to/bpohoriletz/service-objects-----ua-3hf7>

*   Час: 20-30 min
*   рівень:初级/中级
*   код:[GitHub](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/two_level_deep_service_objects)

Перед тим як почати писати дану статтю я спробував знайти інших людейякі намагались розглянути код в даному ракурсі - знайшов наступний пост [How deep is your code?](http://edmundkirwan.com/general/tuples.html)

Мені потрібен був приклад складного Service Object ія знайшов його в репозиторії Discourse [файл](https://github.com/discourse/discourse/blob/482c615ef882c1953070125e0a813683f979e5ff/app/services/user_updater.rb) . Я в жодному разіне критикую даний код, більше того в тому ж самому репозиторії я знайшов чудовий прикладтого що я намагаюсь описати [файл](https://github.com/discourse/discourse/blob/482c615ef882c1953070125e0a813683f979e5ff/app/services/user_merger.rb)

Особисто я надаю перевагу маленьким класам, коротким, single purpose методам,а не одному великому шматку коду. Проте мушу визнати що даний підхідмає значний недолік - досить часто методи короткі лише тому що вони викликають інші методи, які викликають інші методи,які викликають інші методи… Отож, перш ніж я зможу побачити що насправді відбувається,мені доводиться переглядати купу методів і досить часто я вже не пам’ятаю з чого починав, колинарешті знайду те що шукав.

Я не хочу цим займатись, я не хочу переглядати 5-10 приватних методів щобмати уявлення якими будуть наслідки виклику одного публічного методу.я хочу щоб метод одразу, з першого погляду давав зрозуміти що саме відбувається,можливо є спосіб цього досягнути?

Одного разу я бачив метод оцінки складності коду, який описала Sandi Metz в своєму виступі [All the Little Things](https://www.youtube.com/watch?v=8bZh5LMaSmE&t=1892s&index=3&list=PLqwEgoaqsYziFoM8UN2GmWc0BySnWUozK) - the Squint Test.Не зважаючи на те, що даний метод був застосований для оцінки вкладених conditionals, миможемо розглянути виклик методів у схожому контексті. Якщо метод викликає інший методдавайте ми зробимо відступ зліва, так ніби ми маємо вкладений if:

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

Enter fullscreen mode Exit fullscreen mode

тепер давайте запишемо всі задіяні методи і зробимо відступи для кожного вкладеного виклику

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

Enter fullscreen mode Exit fullscreen mode

ми отримали схожу фігуру, що більші нерівною є права її частина - тобільше вкладених викликів ми маємо.

# t0t 1 审评委#1

Зміни в коді можна подивитись [тут](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/a8bf6246520d6ee45db7b1aa708056fa1821de25)

Service Object перед refactoring, я виніс зовнішні залежності в `dependencies.rb` і додав placeholders для задіяних методів

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

Enter fullscreen mode Exit fullscreen mode

# t0t 1 审评委#2

Даний крок поділено на кілька невеликих частин:

*   #2.1 重构`UserUpdater#update` метод ( [提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/60a6e89f7be50eb4b143d6aefa0357e0899e04ae)
*   #2.2 Refactor методи що були додані в 2.1
    *   #2.2.1 重构`UserUpdater#update_user_profile` метод ( [提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/4c383dd20b20addc34b20e6cfa7dc953aac5ad90)
    *   #2.2.2 重构`UserUpdater#update_user` метод ( [提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/d362cdad5cd45c2319fcba961ef92ea85673589f)
    *   #2.2.3 重构`UserUpdater#update_user_option` метод ( [提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/618215dd0d8183c76ebf7549fa8eb9771523bb19)
    *   #2.2.4 重构`UserUpdater#save_user_data` метод ( [提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/3f3fb36a5d502bcb570f4aa150faf2bcee073bba)

Я пропущу деталі, тому що зміни є досить прості і звичні для refactoring, вони були зроблені щоб спростити `UserUpdater#update` метод, зробити його коротким і простим для розуміння.

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

Enter fullscreen mode Exit fullscreen mode

Проте я вважаю, що тепер надто багато інформації прихованоЯкщо подивитись на тіло методу ми більше не бачимо що:

1.  В методі використовується транзакція
2.  Деякі частини змінюються лише за виконання певних умов
3.  Ми покладаємось на константу, коли визначаємо які дані змінити
4.  Не очевидно що ми повертаємо (і використовуємо значення) `true` / `false` в залежності від результату транзакції

In addition to hiding information, I storngly dislike how newОкрім прихованої інформації, мені надзвичайно не подобається як виглядає `UserUpdater#update_user_profile` - це приватний метод, якийскладається лише викликів інших приватних методів і приховує існуючі conditionals.

```
# user_updater.rb
def update_user_profile( user_profile, attributes )
  update_geo_data( user_profile, attributes )
  update_web_data( user_profile, attributes )
  update_background_data( user_profile, attributes )
  update_bio_raw( user_profile, attributes )
end 
```

Enter fullscreen mode Exit fullscreen mode

Я вважаю що схожі методи лише підвищують складність кодуі не мають права існувати.

# t0t 1 审评委#3

Зміни в коді можна подивитись [тут](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/252e123e81951e4a72c7d08dbcbedfbba032a71c)

На цьому кроці ми позбудемось приватних методів, що були (майже) лишеобгортками навколо інших приватних методів і трохи змінимо назви методів, що залишились,щоб краще відобразити їх мету

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

Enter fullscreen mode Exit fullscreen mode

`UserUpdater#update` метод став майже втричі більшим в порівнянні зпопередньою версією, проте тепер він значно краще розповідає свою історію.

Він не ідеальний (і ніколи таким не буде), проте ми зробимо ще один крок для його покращення і знову порівняємо з версією з Кроку #2

# t0t 1 审评委#4

Зміни в коді можна подивитись [тут](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/56690ffe09c4d832feb346456ca00826d8517bd6)

На даному кроці ми ще трохи відшліфуємо наш код і порівняємо остаточні результати

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

Enter fullscreen mode Exit fullscreen mode

Я вважаю що довша версія має наступні переваги:

1.  Код розповідає чудову історію з великою кількістю деталей виконання
2.  Код слідує принципу Open/Closed
3.  Ви завжди щонайбільше за один крок від повного визначення будь-якого приватного методу

# t0t1pids，第一阶段，第二阶段，第二阶段，第一阶段，第二阶段，第二阶段，第二阶段，第三阶段，第二阶段，第三阶段

Для того щоб мати глибину рівну 2 код повинен задовольняти наступні критерії:

1.  Публічні методи не можуть викликати інші публічні методи того ж класу
2.  Приватні/Захищені методи не можуть викликати інші приватні/захищені методи

У мене є ще два не обов’ язкових правила

1.  Уникати conditionals в приватних методах
2.  Conditionals повинні використовувати приватні методи а не boolean expressions

Код:

*   [储存库](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/two_level_deep_service_objects)
*   T0 审评委#1 【t 1
*   T0 审评委# 2.1t 1
*   T0 审评委第 2.2.1 期 T1
*   T0 审评委第 2.2.2 期 T1
*   T0 审评委第 2.2.3 期 T1
*   T0 审裁处# 2.2 . 4t 1
*   T0 审评委#3 审评委 T1
*   T0 审评委第四届会议 T1

# 用于分发的 t0t 1

1.  Дані правила досить просто виконати при написанні Service Objects, тому що вони зазвичайописуюють певний процес/алгоритм - де ще можна застосувати їх?
2.  Як щодо delegated та accessor methods, чи можна їх вважати приватними при застосуванні метрики?