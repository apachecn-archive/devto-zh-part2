# 物联网:使用 Node.js、Elastic Stack 和 MQTT 进行空气污染跟踪

> 原文：<https://dev.to/sematext/iot-air-pollution-tracking-with-nodejs-elastic-stack-and-mqtt-3lmi>

[![](img/09d97862284dd79c20b3220bce15c32e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TU665XIL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/iot-for-environment-protection%402x-1-1024x573.png)

你能用几个物联网设备 Node.js、Elasticsearch 和 MQTT 做什么？你可以像我一样，建立自己的物联网系统来测量空气污染。在这篇博文中，我将分享关于硬件设置、软件配置、数据分析、物联网仪表板以及 MQTT 基于代理与物联网生态系统中其他工具(如 Node-Red 和 Octoblu)的集成的所有细节。当然，我也会分享一些在德国几个地点进行的空气污染物联网传感器测量的有趣发现。看一看——当你使用正确的工具时，做这件事比你想象的要容易得多！

# **动机**

最近[大众排放丑闻](https://en.wikipedia.org/wiki/Volkswagen_emissions_scanda)(维基百科)再次升级。原因是纽约时报报道的[有争议的动物实验。这引发了无数关于 T4 禁止柴油车在我居住的德国城市中心行驶的争论。人们谈论全球汽车禁令，但我很惊讶没有人真正谈论智能城市的概念。除了围绕氮氧化物排放作弊的讨论之外，欧盟还想在德国强制执行颗粒的下限(以 PM10 和 PM2.5 来衡量)。高 PM10 浓度对健康的影响在“](https://www.nytimes.com/2018/01/30/opinion/animal-experiment-volkswagen.html)[环境空气中颗粒的健康影响](https://www.sciencedirect.com/science/article/pii/S143846390470303X)中有所描述。

这就是政治和医学，我们是计算机科学家、数据工程师或开发专家，所以我问自己

***我们能为环保做些什么*？生活在一个数据驱动的决策越来越普遍的世界，收集数据和可视化事实是做出贡献的一种方式。**

发推文到@sematext

正如最近的丑闻所显示的，大公司可能会影响科学研究，游说者正在影响政府，所以为什么不收集开源数据，并基于开放数据或你自己的数据创建独立的分析和独立的观点呢！我们可以帮助提供设备设置、软件配置或在平台中共享数据或分析数据的方法，帮助解释，我们可以在公共场合、聚会、会议等场合谈论它。

对我来说，我只是想在我的环境中看到测量结果，因为公共政府数据只列出了主要城市，他们提供的报告通常都是低分辨率的地图。所以我决定用现成的组件开始一个小小的物联网 DIY 项目，用特定的物质/灰尘传感器来测量空气污染，跟踪 PM10、PM2.5 以及 PM2.5/PM10 比值。我希望能够用移动设备完成这项工作，并在我工作和生活的各个地方进行测量。我的办公室靠近主街道，靠近一个工业区，但我最近搬进了一个农村小镇的新房子，感觉像一个“气候温泉”，实际上有一个疗养胜地。为了让其他人更容易组装像这里描述的物联网系统，我创建了[“空气污染跟踪器”](https://github.com/megastef/AirPollutionTracker)，这样任何人都可以在他们的位置收集数据，试验设置，并分享他们的数据。

# **硬件**

好了，让我们从技术角度出发，首先看看我组装的物联网传感器设备的硬件设置:

[![](img/c1915afae5eb2a09e911999956f00c04.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_9E6GFxX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image10.png)

这就是我们的设置。让我们来看看这款物联网传感器设备的各个部分是什么，有什么作用:

*   使用 Nova SDS011 灰尘传感器测量颗粒物
*   用 GPS 传感器记录测量位置
*   Wi-Fi 连接到我的手机，通过 MQTT 传输测量结果
*   电源组为 Banana-Pi 设备提供电源
*   [Banana-Pi](https://en.wikipedia.org/wiki/Banana_Pi) (比 [Raspberry Pi](https://www.raspberrypi.org/) 功能更强大)带 Debian Linux 和 [Node.js](https://nodejs.org) 用于传感器数据的数据收集和发送

请注意，USB 电源可能不足以支持 GPS、Wi-Fi、PM 传感器和内部以太网接口。

# **软件**

软件架构基于 [MQTT](https://en.wikipedia.org/wiki/MQTT) 消息，旨在扩展到数千台设备，并支持一种简单的方式来实时共享数据以进行任何类型的处理。我们为 Node.js 中的 [@sematext/logagent](https://github.com/sematext/logagent-js) 创建了[开源插件](https://sematext.com/docs/logagent/plugins/)，用于收集和关联来自 *Nova SDS011* 传感器和 GPS 设备的数据。测量结果以 JSON 格式发送给 MQTT 代理，它可以将数据存储在 Elasticsearch 中，或者像我们一样，存储在 Sematext Cloud 中。基于 MQTT 的架构允许其他客户端监听事件流，并在达到 PM10 限制时创建警报或公共 tweets 或控制交通灯。此外，MQTT 消息被记录下来用于历史分析和可视化。

<figure>[![](img/8d5cc1230c2d74a8888791d0f0a64048.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rBlDlV7A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image11.png) 

<figcaption>*建筑*</figcaption>

</figure>

## **吸入新鲜空气并从 PM 传感器收集数据**

该项目从谷歌搜索颗粒物传感器和设备的可用性以及 Node.js 驱动程序开始，因为 Node.js 是我最喜欢的编程语言。经过一番研究，我订购了带 USB 转串行转换器的 *Nova SDS011* 。从串行端口读取值看起来很容易实现，USB 接口在我的 MacBook 和 Banana-Pi 设备上工作正常。下一步是为 [@sematext/logagent](https://sematext.com/logagent/) 创建一个输入插件，将传感器数据注入 logagent 处理管道。Logagent 可以将数据发送到 MQTT、Elasticsearch、Apache Kafka 或简单的文件输出。

我想测量多个位置的空气质量，所以我需要收集测量位置。这可以让我在地图上看到空气污染。最初的方法是在插件配置中添加一个静态的位置信息，但后来我改变了做法，从其他来源获取位置信息，比如 GPS 或通过跟踪我的 iPhone。Nova SDS011 传感器的 [Logagent 插件是开源的，并在 NPM 注册中心发布。Nova SDS011 插件的 Logagent 配置需要模块名称和串行端口名称。或者，您可以使用以分钟为单位的工作周期设置来指定测量收集频率:](https://www.npmjs.com/package/logagent-novasds) 

```
input: 
  novaSDS011: 
    module: input-nova-sda011 
    comPort: /dev/ttyUSB0 
    # persistent setting for measurement interval in minutes 
    workingPeriod: 1 
```

## **获取精确的 GPS 位置**

在设置好串口驱动和 Logagent 之后，第一次实验在我的 MacBook 上开始了。为了在换地方时获得准确的 GPS 位置，我想自动跟踪我的位置。起初，我使用 Logagent 插件 [logagent-apple-location](https://www.npmjs.com/package/logagent-apple-location) 来跟踪我的 iPhone 的位置。为此，我必须扩展 PM 传感器插件来监听“*位置*事件，以便用 GPS 坐标和检索到的地址来丰富传感器数据。这是实验的一个良好开端，直到我的新 gps 设备最终到来，我改用 [logagent-gps](https://www.npmjs.com/package/logagent-gps) 插件来获得独立于互联网连接的精确 GPS 位置。当互联网连接存在时，该插件查询 Google Maps API 来查找当前位置的地址，并使用缓存来避免快速达到 Google API 限制。缓存的缺点是准确性的损失。有了缓存，几百米内的街道号码和地址不会改变。Logagent GPS 插件的配置非常简单。它只需要串行接口的 COM 端口和 npm 模块名:

```
input: 
  gps: 
    module: logagent-gps
    comPort: /dev/ttyACM0 
```

## **计算传感器测量值**

较小的颗粒被认为更危险，因此观察 PM10 和 PM2.5 值的比率可能会很有趣。Nova SDS011 只提供 PM10 和 PM2.5 测量，需要计算 PM2.5/PM10 的比值。请注意，PM2.5 粒子的质量是 PM10 粒子的子集。因此，PM2.5 值总是小于 PM10 值。 [Logagent 支持 JavaScript 函数](https://sematext.com/docs/logagent/filters/#output-filter)用于配置文件中的输入和输出过滤器，所以这就是我们在这里使用的。

```
# calculate PM2.5/PM10 ratio in percent 
outputFilter:
  - module: !!js/function >
      function (context, config, eventEmitter, data, callback)  {
        if (data.PM10 && data.PM10 > 0) {
            data.PM25ratio = (data['PM2.5']/data.PM10) * 100
        }
        callback(null, data)
      } 
```

“数据”变量保存当前的测量值，计算后需要调用回调函数来返回修改后的数据对象。新的数据对象现在包含 PM10、PM2.5 和计算出的 PM2.5 比值！

## **用 MQTT 运送和消费传感器数据**

标准化的 MQTT 协议开销非常小，大多数物联网工具都支持 MQTT。MQTT 使用发布/订阅机制将消息分发到多个客户端。在我们的例子中，传感器设备使用名为“sensor-data”的主题向 MQTT 代理发送 JSON 消息。我们使用 [Logagent MQTT 输出插件](https://sematext.com/docs/logagent/output-plugin-mqtt/)和公共服务 mqtt://test.mosquitto.org。请注意，您应该只在短时间的测试中使用 test.mosquitto.org 服务器。对于生产设置，您应该运行自己的 MQTT 代理。例如，您可以在 Docker 容器中运行[蚊子 MQTT 代理，或者您可以使用](https://hub.docker.com/_/eclipse-mosquitto/) [Logagent MQTT-broker 插件](https://sematext.com/docs/logagent/input-plugin-mqtt-broker/)并运行 Logagent 的另一个实例作为 MQTT 代理。

```
output:  
  mqtt:    
    module: output-mqtt    
    url: mqtt://test.mosquitto.org
    topic: sensor-data
    debug: false
    # optional filter settings matching data field with regular expressions
    filter:
      field: logSource
      match: Nova 
```

现在，我们可以在另一台机器上使用任何 MQTT 客户机，连接到同一个 MQTT 代理，并订阅到达“传感器数据”主题的消息。

如果你想以某种方式处理测量结果或对其采取行动，你可以使用像 [Node-Red](https://nodered.org/) 或 [Octoblu](https://octoblu.github.io/) 这样的工具，并创建物联网工作流。例如，Node-Red 中的 MQTT 插件将 MQTT broker 地址和主题作为参数，因此您可以使用它来订阅“传感器数据”主题，并在启动 Node-Red 指向 MQTT broker 时获取发送到 MQTT broker 的测量值，您将会在 Node-Red 工作流中获取空气污染数据。然后，您根据收到的测量结果执行各种操作。例如，您可以在条件匹配时发布消息，或者根据传感器值改变 LED 灯的颜色，或者您可以控制空调……这里的可能性是无限的！从更大的角度考虑，智能城市可能会选择控制交通，并将空气污染作为交通路线决策的标准之一。节点红色架构可以插入设备、逻辑元件或[神经网络](https://flows.nodered.org/node/node-red-contrib-neuralnet)组件。Node-Red 是一个很好的平台，可以根据空气污染测量结果构建任何逻辑的原型。

<figure>[![](img/de5a3a25f1e72611a8ba187275c1a026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C_RSUHTs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image8.png) 

<figcaption>节点-红色物联网工作流程</figcaption>

</figure>

## **在 Elasticsearch 或 Sematext Cloud 中存储数据**

我们通过 [Logagent Elasticsearch 插件](https://sematext.com/docs/logagent/output-elasticsearch/)将有效的物联网时间序列传感器数据直接存储在 Sematext Cloud 中。Sematext Cloud 为数据、仪表盘和警报提供 Elasticsearch API 兼容端点。Elasticsearch 插件需要 Elasticsearch URL 和索引名。对于 Sematext Cloud，我们使用 Sematext UI 提供的写令牌作为索引名:

```
sematext-cloud:
    module: elasticsearch
    url: https://logsene-receiver.sematext.com
    index: 9eed3c42-1e14-44d2-b319-XXXXXXX 
```

## **香蕉-PI 的完整设备设置**

Banana-PI 设备的设置步骤如下:

1.  创建 Bananian (Debian) SD 卡
2.  通过为 wlan0 接口设置 wpa _–*essid 和 wpa*-password _ in*/etc/network/interfaces*为手机配置 Wi-Fi 卡。在手机上启用网络共享(iPhone 上的“热点”)。
3.  安装 Node.js

```
 curl -sL https://deb.nodesource.com/setup\_8.x | bash - && apt-get install -y nodejs 
```

1.  安装@sematext/logagent 和相关插件

```
 npm i -g --unsafe-perm @sematext/logagent logagent-gps logagent-novasds      npm i -g --unsafe-perm @sematext/logagent-nodejs-monitor      logagent-setup -t YOUR-TOKEN -e [https://logsene-receiver.sematext.com](https://logsene-receiver.sematext.com)        service logagent stop 
```

1.  创建 Logagent 配置(见下文)。使用测试配置

```
logagent --config logagent.conf 
```

将工作配置复制到*/etc/sema text/log agent . conf*，用
启动服务

```
# Example for Logagent configuration
# Plase adjust following settings: 
#   input.novaSDS011.comPort
#   input.gps.comPort
#   input.nodejsMonitor.SPM_TOKEN
#   output.mqtt.url
#   output.elasticsearch.url
#   output.elasticsearch.indices

options: 
  # suppress log event output to console
  suppress: true
  # Write Logagent stats in the Logagent log file /var/log/logagent.log
  # The stats show how many events have been processed and shipped
  # Log interval in seconds
  printStats: 60

input:
  novaSDS011:
    module: input-nova-sda011
    # Find TTY name: ls -l /dev/tty* | grep 'dialout'
    comPort: /dev/ttyUSB0
    # Working period in minutes. The setting is persistent 
    # for the connected Nova SDS011 sensor
    # 1 minute measurement interval
    workingPeriod: 1

  gps: 
    module: logagent-gps
    # Find TTY name: ls -l /dev/tty* | grep 'dialout'
    comPort: /dev/ttyACM0
    # Emit only location event, to share the location with nova sensor
    emitOnlyLocationEvent: true
    # disable debug output
    debug: false

  # Optional, monitor logagent and device performance
  # Create in Sematext Cloud a Node.js monitoring app
  # to obtain the SPM_TOKEN
  nodejsMonitor: 
    module: @sematext/logagent-nodejs-monitor
    SPM_TOKEN: YOUR_SEMATEXT_NODEJS_MONITORING_TOKEN

  # collect all system logs for troubleshooting
  files: 
    - /var/log/**/*.log

# calculate PM2.5/PM10 ratio in percent 
outputFilter:
  - module: !!js/function >
      function (context, config, eventEmitter, data, callback)  {
        if (data.PM10 && data.PM10 > 0) {
            data.PM25ratio = (data['PM2.5']/data.PM10) * 100
        }
        callback(null, data)
      }

output: 
  # print log events in yaml format
  # when options.suppress=false
  stdout: yaml
  # Forward sensor logs to MQTT broker
  mqtt:
    module: output-mqtt
    url: mqtt://test.mosquitto.org
    topic: sensor-data
    debug: false
    # optional filter settings matching data field with regular expressions
    # we use the filter to exclude the system log files
    filter: 
      field: logSource
      match: Nova

  # Store log events & sensor data in Sematext Cloud or Elasticsearch
  # Create a log application in Sematext Cloud to obtain a token
  elasticsearch:
    module: elasticsearch
    url: https://logsene-receiver.sematext.com
    # url: https://logsene-receiver.eu.sematext.com
    # url: http://127.0.0.1:9200 
    # We route system logs and sensor data to different indices
    # each index has a list of regular expressions matching the logSource field
    indices:
      # sensor data index
      YOUR_SEMATEXT_LOGS_TOKEN: 
        - Nova
      # system logs index
      ANOTHER__SEMATEXT_LOGS_TOKEN:
        - var.log.* 
```

## **CPU 和内存占用**

我在 Sematext 做的很多事情都与[性能监控](https://sematext.com/spm/)有关，所以我控制不住自己，不得不查看我的这个 DIY 物联网设置的遥测数据。基于 Node.js 的 Logagent 的低资源使用率令人印象深刻，它只有不到 1%的 CPU 和不到 34 MB 的内存。其他日志工具，如 Logstash，需要 20 倍以上的内存(600 MB 以上),并且会使用 Banana-Pi 或 Raspberry-Pi 等微型计算机上的大部分资源，很快就会耗尽电池！

[![](img/7e5e0bca7af1c5c03314e8412c4ca801.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s9An2h8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image1.png)

如果你像我一样对性能感兴趣，而且如果你想在你的设置出现性能或稳定性问题时得到通知，你可以添加如下所示的 [logagent-nodejs-monitor](https://github.com/megastef/logagent-nodejs-monitor) 插件。最后，我们通过文件输入插件收集所有设备日志来完成配置。/var/log 中的日志文件包含有价值的信息，如 Wi-Fi 状态或 USB 设备信息。

```
input:
  nodejs-monitor:
    module: '@sematext/logagent-nodejs-monitor'
    SPM_TOKEN: 2f3e0e1f-94b5-47ad-8c72-6a09721515d8
  files: 
    - /var/log/**/*.log 
```

我们重新启动 Logagent 来应用配置更改:

```
_service restart logagent_ 
```

几秒钟后，我们将在 Sematext UI 中看到日志和指标。在一个视图中拥有性能指标和日志对于任何类型的故障排除都非常有价值。在我的例子中，USB 线接触不良，丢失的 USB 连接记录在/var/kern.log 中(见截图)。

<figure>[![](img/7f76f7acfb669d422f272539c040f5c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EWKwYYAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image4.png) 

<figcaption>Node.js 性能指标和香蕉-Pi 日志在 Sematext Cloud</figcaption>

</figure>

## **可视化空气污染**

在我们创建可视化之前，我们需要知道传感器/logagent 产生的消息的数据结构。我们可以很容易地将数值绘制成日期直方图，例如 *PM10，【PM2 _ 5】*和*pm25 比率*。可以用地理坐标创建地图。有了每个测量的地址，就很容易找到特定城市的测量，主机名可能有助于我们识别传感器设备。

```
{
  "@timestamp": "2018-02-05T20:59:38.230Z",
  "severity": "info",
  "host": "bananapi",
  "ip": "172.20.10.9",
  "PM2_5": 7.6,
  "PM10": 18,
  "geoip": {
    "location": [
      6.83125466218682,
      49.53914001560465
    ]
  }
  "address": "Weiskirchen, Germany",
  "city": "Weiskirchen",
  "country": "Germany",
  "logSource": "NovaSDS011",
  "PM25ratio": 42.22222222222222,
  "@timestamp_received": "2018-02-05T20:59:58.569Z",
  "logsene_original_type": "logs"
} 
```

*存储在 Elasticsearch / Sematext Cloud 中的 JSON 消息示例*

为了可视化所有数据，我使用了集成在 Sematext Cloud 中的 Kibana。一旦在 Kibana 中创建了可视化，我们就可以创建一个显示地图和传感器值的仪表板。乍看之下，我们可以立即发现，北部(我居住的地方)的空气污染比靠近主要街道的办公室低 50%。

<figure>[![](img/88c2ea7e5cdc31f5a0759f7e9b268fba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YEW5oT_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image13.png) 

<figcaption>基巴纳仪表盘中的语义云</figcaption>

</figure>

## **各种场景下颗粒物浓度的观测**

下图是我从办公室到家的旅途中记录的。图表中的尖峰发生在我停车并从车里拿出测量设备的时候。你可以看到，PM10 值在短时间内跃升至 80 克/平方米(T2 )( T3 ),这是欧盟平均每年 40 克/平方米上限的两倍，尽管只是一分钟。很高兴知道，与办公室相比，我家乡的空气中只有一半的颗粒物——至少只要我不启动我的柴油发动机……总之，这是一个留在家庭办公室的好理由。 [![🙂](img/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png)

[![](img/3c796151c56c5893138fa7b2b0e01116.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KpRM2pCT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image5.png)

<figure>[![](img/bf40fcd5b764fd9ad8b8121e78739c60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zLNDkstv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image3.png)

<figcaption>【PM10】维斯基兴(橙色)和纳尔巴赫(红色)</figcaption>

</figure>

## **【雾霾】【警报】**

拥有仪表板很酷，但是你不能整天看着仪表板。所以让我们使用警报。开源的 ELK 堆栈有其局限性——没有内置警报——但我们可以在 Sematext Cloud 中使用警报。此处保存的查询仅过滤大于 40(欧盟限值)或 50(德国限值)的 PM10 值，用于触发警报:

<figure>[![](img/b1a16ceed9db942e9bd5f2db2f5445ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j0DmqXi9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image14.png) 

<figcaption>为 PM10 值 50 以上设置预警。</figcaption>

</figure>

<figure>[![](img/a1ba01995283c2f13523acb4a30bb732.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r2z1xfFO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image12.png) 

<figcaption>语义界面</figcaption>

</figure>

中的预警通知

有了这样的警报，我们可以将带有警报的事件流(上面的截图)添加到仪表板(下面的截图)，并通过移动电话上的 Slack 频道接收警报。

语义界面中的

<figure>[![](img/40ec4e7f02ee675ddf4141c014e9bade.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R-452d5J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image15.png) 

<figcaption>仪表盘，包括预警通知</figcaption>

</figure>

<figure>[![](img/c7f6fcaa93321482ea020025934c10e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aUXh2bbU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sematext.com/wp-content/uploads/2018/02/image2.png)

<figcaption>PM10 达到 PM10 > 40 或 PM10 > 50</figcaption>

</figure>

的配置阈值时的松弛通知

# **结论**

各种传感器设备的成本都很低，组装这些设备和安装软件只需几个小时。我花了很长时间来为各种小问题找到好的解决方案，并编写了几个 Logagent 插件，但即使编写一个插件模块也只需要几个小时。使用 [Sematext Cloud](https://sematext.com/cloud) 而不是本地 ELK 栈为服务器设置节省了大量时间(我不需要任何物理或云服务器，只需要设备和 Sematext SaaS)。对 Elasticsearch 查询的提醒和对 Slack 的提醒使解决方案更加完整。

这个项目中最大的满足感来源是用“电子鼻”让看不见的东西变得可见——你会觉得自己像一个捉鬼敢死队员！当一扇窗户被打开时，或者当你开始用吸尘器打扫客厅时，或者当你在编程时忘记把意大利面放在炉子上时，你会发现 PM10 值在增加……[![🙂](img/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png)当邻居发动汽车引擎时，当一个游客把车停在你家门前时，当一个客人开始在阳台上抽烟时，外部传感器会“闻到”

一个有趣的事实是，靠近主要街道的 PM10 值更高，实际上在我办公室前面的高峰时间达到了欧盟限制(PM10>40)和德国限制(PM10>50 )!在我的办公室窗口测得的最大值是 PM10=69。在距离主街道几百米的地方，PM10 值会减小。想想意识到这一点会如何影响你的生活决定——搬到一个新的公寓或办公室真的会影响你的健康。了解空气污染最严重的时间也有助于防止微粒进入你的公寓。我的测量显示，在下午 2 点之前和晚上 9 点之后给办公室通风是保持 pm 浓度较低的最好方法。幸运的是，我最近搬到了一个小村庄，我在这里可以找到的好东西是新鲜的空气，正如你所看到的，灵感和新鲜想法的时间！

真正让我吃惊的是，我打电话给市政当局，要求约见市长，与他讨论一旦达到 PM10 限制，交通灯就会变成红色的问题，最终我进入了政界。汽车可以被挡在镇外，因为一条旁路已经存在，但目前没有得到充分利用，应该更多地使用。我希望我能很快安排好与市长的会面，当我与他交谈时，我会有数据来支持我的建议。政府首先要求给我一封书面信件，给我一份正式声明——让我们看看这个星球上是否还有一个智能城市最终做出应用于现实生活的数据驱动型决策，而不仅仅是商业 [![🙂](img/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png) 敬请关注！

帖子 [IoT:用 Node.js、Elastic Stack、MQTT](https://sematext.com/blog/iot-air-pollution-tracking-with-node-js-elastic-stack-and-mqtt/) 进行空气污染追踪最早出现在 [Sematext](https://sematext.com) 上。