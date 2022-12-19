# Python 重试请求

> 原文：<https://dev.to/ssbozy/python-requests-with-retries-4p03>

### 注意自我

*非常感谢[彼得·本特森](https://www.peterbe.com/)T3】*

这将设置一个功能，作为对`requests.get`调用的替代，它可以让您重试请求。

```
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

def requests_retry_session(
    retries=3,
    backoff_factor=0.3,
    status_forcelist=(500, 502, 504),
    session=None,
):
    session = session or requests.Session()
    retry = Retry(
        total=retries,
        read=retries,
        connect=retries,
        backoff_factor=backoff_factor,
        status_forcelist=status_forcelist,
    )
    adapter = HTTPAdapter(max_retries=retry)
    session.mount('http://', adapter)
    session.mount('https://', adapter)
    return session 
```

Enter fullscreen mode Exit fullscreen mode

一些测试总是好的。

```
t0 = time.time()
try:
    response = requests_retry_session().get(
        'http://httpbin.org/status/500',
    )
except Exception as x:
    print('It failed :(', x.__class__.__name__)
else:
    print('It eventually worked', response.status_code)
finally:
    t1 = time.time()
    print('Took', t1 - t0, 'seconds') 
```

Enter fullscreen mode Exit fullscreen mode

### 直觉

这种补偿的算法记录在[这里](https://urllib3.readthedocs.io/en/latest/reference/urllib3.util.html#module-urllib3.util.retry) :
一个补偿因子，在第二次尝试之后的两次尝试之间应用(大多数错误通过第二次尝试立即解决，没有延迟)。urllib3 将休眠:{退避因子} *(2 ^({总重试次数} - 1))秒。如果 backoff_factor 为 0.1，那么 sleep()将休眠[0.0s，0.2s，0.4s，...]重试间隔。它不会比重试时间长。后退 _ 最大值。默认情况下，回退被禁用(设置为 0)。