# 介绍刺激-Flatpickr 包装器📅

> 原文：<https://dev.to/adrienpoly/introducing-stimulus-flatpickr-wrapper--5c23>

自从今年年初发布以来，我真的很喜欢使用刺激方案。这一次，我可以轻松地组织我的 JS 代码，而不需要一个成熟的前端框架。

Stimulus-flatpickr wrapper 作为一个实验开始，我需要在一个项目中使用 flatpickr，并为它构建一个刺激控制器。我很快意识到为这个库配备一个通用的刺激控制器是有意义的。

今天，我们将通过一个简单而有趣的例子来演示这个包。我最近用日期选择器工作了一段时间，意识到将一个字段转换成日期选择器总是非常简单的，但是要使它成为一个真正易于使用的解决方案，还需要很多小细节。

**目标是创建一个基本的预订系统，用户:**

*   只能选择可用日期(已经预订的日期必须禁用)
*   获取以他的语言显示的日期选择器
*   查看区域设置格式的输入字段中的日期
*   查看所有预订的列表
*   所有这些都在一个页面应用程序中得到了很好的体现

栈相当简单:Rails、Stimulus、Turbolinks 和用于日期选择解决方案的 Flatpickr。

最终，我们将不再需要任何 Ajax，只需要不到 10 行的 Javascript 代码就可以做到这一点🎉🎉🎉

## 让我们开始:新应用程序和刺激设置

这里有几个教程，我不会涉及所有的细节，但是如果你已经安装了 webpack，在你的 Rails 应用程序中设置激励就是这么简单:

```
rails webpacker:install:stimulus 
```

Enter fullscreen mode Exit fullscreen mode

这将向您的`package.json`添加刺激包，在您的主`application.js`文件中添加初始化代码，并在 javascript 下为您所有的新`Stimulus`控制器添加一个新的控制器目录

你可以阅读这篇文章来了解更多关于在你的 rails 中设置激励的细节

在今天的演示中，我们将创建一个全新的应用程序

```
 rails new --webpack=stimulus stimulus-flatpickr 
```

Enter fullscreen mode Exit fullscreen mode

...别忘了添加包装

```
 #app/views/layouts/application.html.erb
  ...
  <%= stylesheet_pack_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
  <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
  ... 
```

Enter fullscreen mode Exit fullscreen mode

## 预约模式和控制器

我们正在做一个预订引擎。我们需要一个具有单列 start_at date 和一个用于获取接下来 n 天的`up_comings`约会的`scope`方法的约会模型(我们只能预订接下来 60 天的约会)。

```
#models/appointment.rb
class Appointment < ApplicationRecord
  validates :start_at, uniqueness: true

  scope :up_comings, ->(nb_days) {
                       where('start_at >= ? AND start_at < ?',
                             Time.zone.now,
                             Time.zone.now + nb_days.days).order(start_at: :asc)
                     }
end 
```

Enter fullscreen mode Exit fullscreen mode

我们的控制器非常标准(为了简单起见，我们在这里只处理成功路径👶无错误管理)

```
class AppointmentsController < ApplicationController
  before_action :set_appointment, only: %i[update destroy]

  def index
    @appointments = Appointment.up_comings(60)
    @appointments_dates = @appointments.pluck(:start_at)
    @appointment = Appointment.new
  end

  def create
    redirect_to appointments_path if Appointment.create(appointment_params)
  end

  def update
    redirect_to appointments_path if @appointment.update(appointment_params)
  end

  def destroy
    @appointment.destroy
    redirect_to appointments_path
  end

  private

  def set_appointment
    @appointment = Appointment.find(params[:id])
  end

  def appointment_params
    params.require(:appointment).permit(:start_at)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 刺激-flatpickr 控制器

根据[文档](https://github.com/adrienpoly/stimulus-flatpickr#advanced-usage)，我们将创建一个刺激控制器，它将扩展通用刺激-flatpickr 控制器。

首先，让我们添加我们将需要的包

```
 yarn add stimulus-flatpickr 
```

Enter fullscreen mode Exit fullscreen mode

并添加新的控制器

```
// ./controllers/flatpickr_controller.js
import Flatpickr from "stimulus-flatpickr";
import "flatpickr/dist/themes/dark.css";

// creates a new Stimulus controller by extending stimulus-flatpickr wrapper controller
export default class extends Flatpickr {} 
```

Enter fullscreen mode Exit fullscreen mode

## 观点

让我们放一个小结构，左边是预订表单，右边是即将到来的约会列表。

```
#app/views/appointments/index.html.erb
<div class="main">
  <h2><%= t ".title" %></h2>
  <div class="row">
    <div class="col">
      <h3><%= t ".new" %></h3>
      <%= render "form", appointment: @appointment %>
    </div>
    <div class="col">
      <h3><%= t ".appointments" %></h3>
      <%= render @appointments %>
    </div>
  </div>
</div>

#app/views/appointments/_appointment.html.erb
<div class="appointments">
  <%= render "form", appointment: appointment %>
  <%= link_to "x", appointment_path(appointment), method: :delete%>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

以及我们的形式，所有的魔法都将在那里发生🎉

```
#app/views/appointments/_form.html.erb
<%= form_with model: appointment do |f| %>
  <%= f.text_field :start_at,
      data: {
        controller: "flatpickr",
        flatpickr_min_date: Time.zone.now,
        flatpickr_max_date: Time.zone.now + 60.days,
        flatpickr_disable: Appointment.up_comings(60).pluck(:start_at),
      } %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

**这是理解**的重要部分:

我们定义了一个`flatpickr_controller.js`，所以每当一个 HTML 元素有一个`data-controller="flatpickr"`属性时，刺激控制器就会开始动作。

因此，在视图中，`data: { controller: "flatpickr" }`将被转换为`data-controller="flatpickr"`,从而将该字段转换为 datepicker。

我们还可以使用相同的数据属性(`data-flatpickr-the-kebab-case-option-name)`)将选项传递给 datepicker。在这里，我们设置了一个最小和最大日期来禁用今天之前的所有内容和今天+ 60 天之后的所有内容。

此外，我们为下一步做准备，我们将通过传递当前预订的数组来禁用已经预订的日期。

## 选择时提交

让用户选择日期时自动提交(不完美的 UX！我知道，这主要是为了简单的演示目的)。
flatpickr 控制器具有所有可用的官方 flat pickr 挂钩(打开、关闭、更改等)。在这里，当值发生变化时，我们需要提交表单。所以让我们重写`change()`函数。

```
export default class extends Flatpickr {
  // automatically submit form when a date is selected
  change(selectedDates, dateStr, instance) {
    const form = this.element.closest("form");
    Rails.fire(form, "submit");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 即将上线

此时我们有以下结果:

[![stimulus-flatpickr demo](img/b16fcdab74350f9dd37a0f9edf2e0262.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mCFSMUQY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x73wolvxxqxovspv2cdu.gif)

我们开始有一些互动....酷的是默认安装的 Turbolinks 会自动启动并`Ajaxify`所有链接。所有的刺激控制器都是与 Turbolinks 一起设计的。**所以这里不用做别的，就是管用！**。没有自定义 Ajax 调用或 SRJ 来获得这个 SPA 的外观和感觉。💪 🚀 ❤️

## 本地化日期选择器和日期格式

我们有一个水疗外观和感觉，以及我们的预订引擎的可用性。下一步是正确地将其本地化。目前，它只有英语和日期格式，如果相当难看`2018-09-12`👎。

#### 日期格式

stimulus-flatpickr 包装器提供了一个比标准库更好的**额外特性**，它将把`strftime`日期格式转换成 flatpickr 自定义日期格式。

因此，为了定制日期格式，我们可以将本地格式直接传递给日期选择器，如下所示:

```
data: {
  controller: "flatpickr",
  flatpickr_alt_input: true,
  flatpickr_alt_format: t("date.formats.long"),
  flatpickr_default_date: appointment.start_at,
  flatpickr_disable: @appointments_dates - [appointment.start_at],
  flatpickr_min_date: Time.zone.now,
  flatpickr_max_date: Time.zone.now + 60.days,
} 
```

Enter fullscreen mode Exit fullscreen mode

当区域设置为`:en`时，`flatpickr_alt_format: t("date.formats.long")` - >将输出`"%B %d, %Y"`，当区域设置为`:fr`时，`"%e %B %Y"`将自动转换为最近的 flatpickr 格式。尽可能的干燥🎉！

#### 翻译

我们现在几乎什么都有了。最后一点是正确翻译每个地区的 datepicker。

我们将在刺激控制器中导入不同的地区。每当 Turbolinks 无声地替换页面的内容时，刺激控制器的`initialize()`函数就被调用。这是我们将要设置局部变量并将其传递给 flatpickr 的地方。

我们最终的控制器是这样的

```
import Flatpickr from "stimulus-flatpickr";

// import a theme (could be in your main CSS entry too...)
import "flatpickr/dist/themes/dark.css";

// import the translation files and create a translation mapping
import { French } from "flatpickr/dist/l10n/fr.js";
import { english } from "flatpickr/dist/l10n/default.js";

// create a new Stimulus controller by extending stimulus-flatpickr wrapper controller
export default class extends Flatpickr {
  locales = {
    fr: French,
    en: english
  };

  initialize() {
    //set the locale and also sets the global flatpickr settings 
    this.config = {
      locale: this.locale,
      altInput: true,
      showMonths: 2,
      animate: false
    };
  }

  // automatically submit form when a date is selected
  change(selectedDates, dateStr, instance) {
    const form = this.element.closest("form");
    Rails.fire(form, "submit");
  }

  get locale() {
    if (this.data.has("locale")) {
      return this.locales[this.data.get("locale")];
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 那都是乡亲们！

[![stimulus-flatpickr demo](img/bd4d77c7299767ce1d56117e15deb87a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EkAlC1qA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i0xi1gv0keumrynau78r.gif)

你可以在这里找到整个演示项目👉[https://github.com/adrienpoly/rails_stimulus_flatpickr](https://github.com/adrienpoly/rails_stimulus_flatpickr)

**更重要的**刺激-flatpickr 包装器👉https://github.com/adrienpoly/stimulus-flatpickr

我希望你喜欢这个介绍。我个人认为，我们将会看到越来越多的标准刺激控制器，Rails 社区将会越来越多地放弃只用于前端包的宝石。

我不是经验丰富的高级开发人员，所以请随意**评论、问题、公关**。他们都是受欢迎的，如果你觉得这个包是有用的，离开它一个明星⭐

快乐编码🎉