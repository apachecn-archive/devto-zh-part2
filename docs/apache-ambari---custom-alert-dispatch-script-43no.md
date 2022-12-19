# Apache Ambari -自定义警报调度脚本

> 原文：<https://dev.to/risdenk/apache-ambari---custom-alert-dispatch-script-43no>

### 概述

Apache Ambari 让管理像 [Apache Hadoop](https://hadoop.apache.org/) 这样的分布式系统变得更加容易。安巴里的能力之一是[警报](https://cwiki.apache.org/confluence/display/AMBARI/Alerts)。Ambari 监控集群的状态，并可以根据 web 端点不可用或定制脚本的输出发出警报。当警报被触发时，Ambari 会在 UI 中显示一个通知，而且还提供发送电子邮件、SNMP 或调用自定义脚本的能力。

### 集成 Ambari 警报和外部工具

[Ambari 定制脚本调度](https://cwiki.apache.org/confluence/display/AMBARI/Creating+a+Script-based+Alert+Dispatcher)功能使得将警报与外部工具集成成为可能。我们使用一个定制的 Python 脚本将警报发送到一个共享邮箱，并在必要时进行呼叫。这是很灵活的，在初始设置后，无需重启 Ambari 服务器就可以更改脚本。另一个好处是为每个警报单独调用脚本。警报不会像内置的电子邮件通知程序那样进行批处理。

### 创建自定义的 Ambari 预警调度程序

以下内容摘自下面的[帖子](https://community.hortonworks.com/content/supportkb/48921/how-to-use-script-based-alert-dispatchers-in-ambar.html)，添加了细节，因为不清楚脚本的格式应该是什么。我们还发现，随着时间的推移，脚本 API 略有变化，向脚本添加了更多的参数(例如: [AMBARI-20291](https://issues.apache.org/jira/browse/AMBARI-20291) )。

#### 创建 Ambari 自定义警报调度程序脚本

```
#!/usr/bin/env python

from datetime import datetime
import sys

def handle_alert():
  '''
  # handle_alert method which is called from Ambari
  # :param definitionName: the alert definition unique ID
  # :param definitionLabel: the human readable alert definition label
  # :param serviceName: the service that the alert definition belongs to
  # :param alertState: the state of the alert (OK, WARNING, etc)
  # :param alertText: the text of the alert
  # :param alertTimestamp: the timestamp the alert went off - Added in AMBARI-20291
  # :param hostname: the hostname the alert fired off for - Added in AMBARI-20291
  '''

  definitionName = sys.argv[1]
  definitionLabel = sys.argv[2]
  serviceName = sys.argv[3]
  alertState = sys.argv[4]
  alertText = sys.argv[5]
  # AMBARI-20291
  if len(sys.argv) == 8:
    alertTimestamp = sys.argv[6]
    hostname = sys.argv[7]
  else:
    alertTimestamp = 'N/A'
    hostname = 'N/A'

  # Generate a timestamp for when this script was called
  timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')

  # Add custom logic here to handle the alert

if __name__ == ' __main__':
  if len(sys.argv) >= 6:
    handle_alert()
  else:
    print("Incorrect number of arguments")
    sys.exit(1) 
```

Enter fullscreen mode Exit fullscreen mode

#### 设置安巴里预警目标

*   `vi /etc/ambari-server/conf/ambari.properties`
    *   `my.custom.alert.dispatcher.script=PATH_TO/ambari_custom_alert_dispatcher.py`
*   `ambari-server restart`

#### 创建安巴里预警目标

```
curl -i \
  -u $(whoami) \
  -H 'X-Requested-By: ambari' \
  -XPOST \
  "https://AMBARI_SERVER_HOST:8443/api/v1/alert_targets" \
  -d '
  {
    "AlertTarget": 
      {
        "name": "my_custom_dispatcher", 
        "description": "My Custom Dispatcher", 
        "notification_type": "ALERT_SCRIPT", 
        "global": true, 
        "alert_states": ["CRITICAL"], 
        "properties": { 
          "ambari.dispatch-property.script": "my.custom.alert.dispatcher.script"
        }
      }
  }
' 
```

Enter fullscreen mode Exit fullscreen mode

#### 删除安巴里预警目标

```
curl -i \
  -u $(whoami) \
  -H 'X-Requested-By: ambari' \
  -XDELETE \
  "https://AMBARI_SERVER_HOST:8443/api/v1/alert_targets/ALERT_NUMBER" 
```

Enter fullscreen mode Exit fullscreen mode