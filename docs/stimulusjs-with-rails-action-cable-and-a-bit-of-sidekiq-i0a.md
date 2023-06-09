# StimulusJS 带有 Rails Action 电缆和一点 Sidekiq

> 原文：<https://dev.to/dstull/stimulusjs-with-rails-action-cable-and-a-bit-of-sidekiq-i0a>

# 目标

用名为 [StimulusJS](https://stimulusjs.org/) 的适度 js 框架展示了 [Rails Action Cable](https://guides.rubyonrails.org/action_cable_overview.html) 的简单实现。

对于本教程，我将使用一个简单的演示 Rails 应用程序，您可以在这里找到[的源代码。](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/tree/actioncable)

在这里，我将忽略掉 Sidekiq 的细节，而把重点放在刺激和动作电缆上。我相信这将是最有价值的，因为其他部分已经在许多博客和教程中多次提到。但是，我可能会在以后重新讨论其他项目。

我将把它分成两步:

*   创建 Sidekiq 工人和行动电缆通道
*   设置刺激 JS

# 创建 Sidekiq 工作器和动作电缆通道

对于这一部分，我想展示当一个司机的名字或他们所属的特定汽车发生变化时，如何更新一个列出有许多司机的汽车的索引页面。

为了在这个例子中实现这一点，我决定让 Sidekiq 作业在汽车模型上触发 off 和`after_touch`回调。

## 汽车/司机车型-活动记录车型

```
class Car < ApplicationRecord
  has_many :drivers, dependent: :destroy

  after_touch :update_driver_names

  def drivers_list
    drivers.pluck(:name).join(',')
  end

  private

  def update_driver_names
    CarsWorker.perform_async(id)
  end
end

class Driver < ApplicationRecord
  belongs_to :car, touch: true

  delegate :name, to: :car, prefix: true
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的文件中，我通过将`touch: true`添加到驾驶员模型来触发汽车模型上名为`update_driver_names`的`after_touch`回调。
`update_driver_names`方法到达 Sidekiq 并调用一个名为`CarsWorker.perform_async`的异步作业，发送司机分配的汽车的`id`。

## cars Worker([cars _ Worker . Rb](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/blob/actioncable/app/workers/cars_worker.rb))-Sidekiq Worker

```
class CarsWorker
  include Sidekiq::Worker

  def perform(car_id)
    # some contrived work...
    car                = Car.find car_id
    new_driver_changes = car.driver_changes + 1
    car.update_attribute(:driver_changes, new_driver_changes)

    car_drivers = car.drivers_list
    ActionCable.server.broadcast('cars', drivers: car_drivers, car_id: car_id, driver_changes: new_driver_changes)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在上述文件中，我是:

1.  增加驾驶员汽车上的`driver_changes`并在汽车上提交。
2.  找到那辆车的所有司机，并向 Action Cable 频道广播新司机列表，作为一个字符串放在`car_drivers`中，以及`driver_changes`的编号。

下面是来自汽车频道的传输示例:

```
CarsChannel transmitting {"drivers"=>"Jacalyn Bauchblah,Wes Goodwin,Guy Keeling,Miss Pasquale Doyle,Candy Welch", "car_id"=>23, "driver_changes"=>4} (via streamed from cars) 
```

Enter fullscreen mode Exit fullscreen mode

## 【cars Channel】([cars _ Channel . Rb](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/blob/actioncable/app/channels/cars_channel.rb))-动作索通道定义

```
class CarsChannel < ApplicationCable::Channel
  def subscribed
    stream_from 'cars'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

以上仅仅是标准的样板通道定义，这里定义的是。

# 设置刺激 JS

对于这一部分，我将展示 HTML erb 片段和 StimulusJS 设置。

## 汽车索引([汽车/Index . HTML](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/blob/actioncable/app/views/cars/index.html.erb))-HTML 片

```
<p id="notice"><%= notice %></p>

<div class="page-header" data-controller="cars">
  <h1>Cars</h1>
</div>

<table class="table table-hover">
  <thead>
  <tr>
    <th>Name</th>
    <th>Drivers</th>
    <th>Driver Changes</th>
    <th>Make</th>
    <th>Color</th>
    <th>Model</th>
    <th colspan="3"></th>
  </tr>
  </thead>

  <tbody>
  <% @cars.each do |car| %>
    <tr id="car_id_<%= car.id %>">
      <td><%= car.name %></td>
      <td class="cars--drivers"><%= car.drivers_list %></td>
      <td class="cars--driver-changes"><%= car.driver_changes %></td>
      <td><%= car.make %></td>
      <td><%= car.color %></td>
      <td><%= car.model %></td>
      <td><%= link_to 'Show', car %></td>
      <td><%= link_to 'Edit', edit_car_path(car) %></td>
      <td><%= link_to 'Destroy', car, method: :delete, data: { confirm: 'Are you sure?' } %></td>
    </tr>
  <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Car', new_car_path %> 
```

Enter fullscreen mode Exit fullscreen mode

上面要指出的重要部分是`data-controller`数据属性。将此设置为 StimulusJS 控制器的名称`cars`，将导致它在页面呈现时伸出手并调用`cars_controller.js`连接函数；为用户订阅汽车行动有线频道。在 StimulusJS [网站](https://stimulusjs.org/)上有解释该部分如何工作的文档。

## 汽车控制器([汽车 _ 控制器. js](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/blob/actioncable/app/javascript/controllers/cars_controller.js))-stimulus js 控制器

```
import { Controller } from 'stimulus';
import createChannel from '../exports/cable';

export default class extends Controller {
  connect() {
    this.initChannel();
  }

  initChannel() {
    createChannel('CarsChannel', {
      received(data) {
        const carRow = $(`#car_id_${data.car_id}`);
        const driverChanges = carRow.find('.cars--driver-changes');
        const drivers = carRow.find('.cars--drivers');
        driverChanges.text(data.driver_changes);
        drivers.text(data.drivers);
      },
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面，我们:

1.  从`exports/cable.js`导入基本电缆设置
2.  初始化 connect 函数的通道，由于`data-controller`数据属性，每当我们进入 cars 索引页面时都会触发这个函数。
3.  在`CarsChannel`上收到消息时，更新 cars 索引页面。

## Cable Javascript([Cable . JS](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/blob/actioncable/app/javascript/exports/cable.js))-需要时导入的基本动作 Cable JS 设置

```
import cable from 'actioncable';

let consumer;

export default function (...args) {
  if (!consumer) {
    consumer = cable.createConsumer();
  }

  return consumer.subscriptions.create(...args);
} 
```

Enter fullscreen mode Exit fullscreen mode

以上是初始/标准动作电缆设置。我在这里做了“额外的努力”,还用 yarn 安装了 [actioncable](https://github.com/dstull/sidekiq-actioncable-stimulus-demo/blob/actioncable/yarn.lock#L166) ,确保它和 Rails 是同一个版本。这有助于我完全脱离资产管道/链轮领域。

# 在关闭...

我特别想完全摆脱 Rails 资产管道/链轮设置，并针对我的渠道订阅进行页面定制。

希望这个小演示对某个人有帮助。我搜索了很多地方来收集如何将这些串在一起的点点滴滴，并觉得我应该与社区分享我从自己身上受益匪浅。我只有几个小时的时间把这些拼凑起来，然后就要回去做父母了:)...也许以后我会添加一个博客，介绍我是如何从头到尾实施测试的。

这项工作的一些基础来自邪恶的火星人的精彩博客。