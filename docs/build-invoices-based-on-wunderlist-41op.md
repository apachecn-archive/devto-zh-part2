# 基于 Wunderlist 构建发票

> 原文：<https://dev.to/oivoodoo/build-invoices-based-on-wunderlist-41op>

在过去的 8 年里，我在远程工作，为按小时收费的客户签订合同，这需要每 2 周每小时发送一次发票。我试图使用不同的应用程序来开发票，但后来我离开了，使用谷歌文档来创建它。

然后我找到了 Wunderlist，一个不错的待办事项应用程序。我创建了购物和待办事项清单，并开始每天使用。结果，在那里写作成了我的一种习惯。我觉得记录我在那里的时间也很棒。

任务时间格式示例:

```
<hours> hours TASK-1
<minutes> minutes TASK-2
<days> days TASK-3 
```

Enter fullscreen mode Exit fullscreen mode

一个月后，我决定下载这个列表并计算这个月花费的时间。因此，我编写了将小时报告下载到 csv 文件中的脚本。

```
import wunderpy2

from dateutil import parser
from datetime import date
from csv import writer

ACCESS_TOKEN = '<WUNDERLIST_ACCESS_TOKEN>'
CLIENT_ID = '<WUNDERLIST_CLIENT_ID>'
PROJECT_BOARD = 'LIST_TITLE'
HOUR_RATE = 1

def __build_hour(task):
    '''
    Example:
        dict(title='1 hour CORE-1234')
        dict(title='5 hours CORE-4321')
        dict(title='1 minute CORE-1234')
    '''
    columns = task['title'].strip().split(' ')
    time, measure, task_id = columns
    if measure == 'hour' or measure == 'hours':
        hours = float(time)
    elif measure == 'minute' or measure == 'minutes':
        hours = float(time) / 60
    elif measure == 'day' or measure == 'days':
        hours = 24 * float(time)
    else:
        raise 'unknown measure from the task title: ' + task['title']

    return dict(
        task_id=task_id,
        created_at=parser.parse(task['created_at']),
        hours=hours)

def __main__():
    api = wunderpy2.WunderApi()
    client = api.get_client(ACCESS_TOKEN, CLIENT_ID)
    board = [l for l in client.get_lists() if l['title'] == PROJECT_BOARD][0]
    active_tasks = [t for t in client.get_tasks(board['id']) if not t['completed']]
    report = [__build_hour(task) for task in active_tasks]

    estimated_earnings = sum([task['hours'] for task in report]) * HOUR_RATE
    print(estimated_earnings)

    with open(f"report-{date.today()}.csv", 'w+') as f:
        w = writer(f)
        rows = [[task['task_id'], task['hours'], task['created_at'].date()]
                for task in report]
        w.writerows(rows)

    for task in active_tasks:
        client.update_task(task['id'], task['revision'], completed=True)

    # TODO: make monthly output report to pdf file based on created_at field. 

if __name__ == '__main__':
    __main__() 
```

Enter fullscreen mode Exit fullscreen mode