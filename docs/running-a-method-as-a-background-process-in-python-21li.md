# 在 Python 中将方法作为后台进程运行

> 原文：<https://dev.to/hasansajedi/running-a-method-as-a-background-process-in-python-21li>

下面是在 Python 中作为后台线程运行类方法的一小段代码。run()方法永远做一些工作，在这个用例中，您希望它在后台做这些工作。而应用程序的其余部分继续工作。

```
import time

import threading

class TestThreading(object):
    def __init__(self, interval=1):
        self.interval = interval

        thread = threading.Thread(target=self.run, args=())
        thread.daemon = True
        thread.start()

    def run(self):
        while True:
            # More statements comes here
            print(datetime.datetime.now().__str__() + ' : Start task in the background')

            time.sleep(self.interval)

tr = TestThreading()
time.sleep(1)
print(datetime.datetime.now().__str__() + ' : First output')
time.sleep(2)
print(datetime.datetime.now().__str__() + ' : Second output') 
```

## 以下是输出:

> 2018-08-18 13:39:59.021000:后台启动任务
> 2018-08-18 13:40:00.036000:首次输出
> 2018-08-18 13:40:00.036000:后台启动任务
> 2018-08-18 13:40:01.036000:启动