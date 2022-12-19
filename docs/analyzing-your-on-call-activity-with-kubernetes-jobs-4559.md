# 使用 Kubernetes Jobs 分析您的随叫随到活动

> 原文：<https://dev.to/jmarhee/analyzing-your-on-call-activity-with-kubernetes-jobs-4559>

在 Kubernetes 中，您可以创建作业或一次性任务，或者使它们成为重复的 CronJob 资源(就像在非分布式系统上一样)，以调度 pods 来完成任务。

让我们以下面的脚本为例:

```
import requests
import json
import os
import datetime
import sys

API_KEY = os.environ['PAGERDUTY_RO_KEY']

SINCE = os.environ['START_DATE']
UNTIL = os.environ['END_DATE']
STATUSES = []
TIME_ZONE = 'UTC'
LIMIT = 50
RUNDATE = datetime.datetime.today().strftime('%Y%m%d%H%M%S')

def list_incidents(offsetval):
 url = 'https://api.pagerduty.com/incidents'
 headers = {
 'Accept': 'application/vnd.pagerduty+json;version=2',
 'Authorization': 'Token token={token}'.format(token=API_KEY)
 }
 payload = {
 'since': SINCE,
 'until': UNTIL,
 'statuses[]': STATUSES,
 'limit': LIMIT,
 'time_zone': TIME_ZONE,
 'offset': offsetval
 }
 r = requests.get(url, headers=headers, params=payload)
 return r.text

def write_csv(resp):
 incidents = json.loads(resp)['incidents']
 incidents_data = open('/mnt/pd-audit/%s-Incidents-%s.csv' % ( RUNDATE, offset), 'w+')
 for inc in incidents:
 incidents_data.write("%s,%s,\n" % (inc['title'],inc['created_at']))
 incidents_data.close()

if __name__ == '__main__':
 more_status = True
 offset = 0
 while more_status == True:
 resp = list_incidents(offset)
 more = json.loads(resp)['more']
 if more == False:
 more_status = False
 print "No more pages after current run. '/mnt/pd-audit/%s-Incidents-%s.csv'..." % (RUNDATE, offset)
 write_csv(resp)
 else:
 print "Writing '/mnt/pd-audit/%s-Incidents-%s.csv'..." % (RUNDATE, offset)
 resp = list_incidents(offset)
 write_csv(resp)
 offset += 1 
```

它只是将开始和结束日期之间的 PagerDuty 活动的 CSV 转储到磁盘，您的团队可以在以后查看它。

因此，假设您想在 Kubernetes 上运行这个任务，您可以根据需要启动它，将这个脚本构建成一个图像:

```
FROM python:2

ENV PAGERDUTY_RO_KEY
ENV START_DATE
ENV END_DATE

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY pd-alert-csv.py main.py

CMD ['python','main.py'] 
```

并将其推送到您的注册表中，然后您可以在模板中引用它，如下所示:

```
apiVersion: batch/v1
kind: Job
metadata:
 name: pd-alert-csv
 namespace: jobs-space
spec:
 template:
 spec:
 volumes:
 - name: pd-data
 emptyDir: {}
 containers:
 - name: pd-alert-csv
 image: coolregistry.us/pd-alert-csv:latest
 volumeMounts:
 - mountPath: "/mnt/pd-audit"
 name: pd-data
 env:
 - name: PAGERDUTY_RO_KEY
 valueFrom:
 secretKeyRef:
 name: pd-api-token
 key: PAGERDUTY_RO_KEY
 - name: START_DATE
 value: "SOME_DATE" 
#Ideally, you might program this window calculation into the script if this were recurring
 - name: END_DATE
 value: "SOME_LATER_DATE" 
```

您可能需要为数据准备一个 EBS 支持的卷，或者按照您希望的方式存储它(或者甚至直接将它写入 S3 或脚本中的内容)，然后将您的 PagerDuty API 键作为环境变量加载到 pod 中，作为 Kubernetes 中的一个秘密，然后将作业的开始和结束日期从我们刚刚构建的映像传递到您的容器中。

为了使这成为一项重复的工作，就像部署资源对 Pods 所做的那样，您将把您的工作包装在一个 CronJob 声明中，以扩展 Kubernetes 应该对该工作做什么以及何时做:

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
 name: pd-alert-report-cron
spec:
 schedule: "0 \*/12 \* \* \*"
 jobTemplate:
 spec:
 template:
 spec:
... 
```

然后在该规范键中缩进的将是我们上面定义的作业。

了解更多关于作业(工作队列等)的操作。)，请查看 Kubernetes 文档:

[https://kubernetes . io/docs/concepts/workloads/controller/jobs-run-to-completion/](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)

还有**惊人的 Kubernetes By Example** 另一个很棒的例子:

[http://kubernetesbyexample.com/jobs/](http://kubernetesbyexample.com/jobs/)