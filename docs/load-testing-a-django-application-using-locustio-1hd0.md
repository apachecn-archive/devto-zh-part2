# 使用 LocustIO 对 Django 应用程序进行负载测试

> 原文：<https://dev.to/apcelent/load-testing-a-django-application-using-locustio-1hd0>

Django 框架，用于以简洁高效的方式快速构建 web 应用程序。随着应用程序规模的增加，所有团队面临的一个共同问题是应用程序的性能。衡量绩效和分析改进领域是交付高质量产品的关键。

[LocustIO](https://github.com/locustio/locust) ，用 python 编写的开源工具，用于 web 应用的[负载测试](https://en.wikipedia.org/wiki/Load_testing)。使用 web 用户界面查看测试结果简单而容易。它是可伸缩的，可以分布在多台机器上。

本文展示了一个使用 locust 对 django web 应用程序进行负载测试的例子。

在开始负载测试之前，我们必须决定要测试的页面。在我们的例子中，我们希望用户按照他们登录的场景，访问不同的页面并提交受 CSRF 保护的表单。

LocustIO 帮助我们模拟用户在我们的 web 应用程序上执行这些任务。测量性能的基本思想是对不同的任务进行多次请求，并分析这些请求的成功和失败。

## 安装

```
pip install locustio 
```

Enter fullscreen mode Exit fullscreen mode

LocustIO 仅支持 python 2.x。目前不支持 python 3.x。

## 蝗虫文件

创建 Locust 文件是为了模拟 web 应用程序用户的操作。

```
from locust import HttpLocust, TaskSet, task

class UserActions(TaskSet):

    def on_start(self):
        self.login()

    def login(self)
        # login to the application
        response = self.client.get('/accounts/login/')
        csrftoken = response.cookies['csrftoken']
        self.client.post('/accounts/login/',
                         {'username': 'username', 'password': 'password'},
                         headers={'X-CSRFToken': csrftoken})

    @task(1)
    def index(self):
        self.client.get('/')

    for i in range(4):
        @task(2)
        def first_page(self):
            self.client.get('/list_page/')

    @task(3)
    def get_second_page(self):
        self.client.('/create_page/', {'name': 'first_obj'}, headers={'X-CSRFToken': csrftoken})

    @task(4)
    def add_advertiser_api(self):
        auth_response = self.client.post('/auth/login/', {'username': 'suser', 'password': 'asdf1234'})
        auth_token = json.loads(auth_response.text)['token']
        jwt_auth_token = 'jwt '+auth_token
        now = datetime.datetime.now()

        current_datetime_string = now.strftime("%B %d, %Y")
        adv_name = 'locust_adv' 
        data = {'name', current_datetime_string}
        adv_api_response = requests.post('http://127.0.0.1:8000/api/advertiser/', data, headers={'Authorization': jwt_auth_token})

    class ApplicationUser(HttpLocust):
        task_set = UserActions
        min_wait = 0
        max_wait = 0 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，locust 文件定义了用户执行的一组 4 个任务——登录后导航到主页，多次访问列表页面，一次提交表单。

参数 min_wait 和 max_wait 定义了不同用户请求之间的等待时间。

## 跑蝗

导航到 locustfile.py 目录并运行。

```
locust --host=<host_name> 
```

Enter fullscreen mode Exit fullscreen mode

应用程序的 URL 在哪里？

Locust 实例在本地运行于 [http://127.0.0.1:8089](http://127.0.0.1:8089)

当一个新的测试开始时，locust web UI 提示输入要模拟的用户数和孵化率(每秒用户数)。

我们首先尝试以每秒 1 个用户的孵化率模拟 5 个用户，观察结果。

[![Alt text of image](img/c10d4e1a1501ca4a082a3370c511c64e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4-KX3kVJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/locust_users.png)

一旦测试开始，locustIO 将执行所有任务，并记录请求成功/失败的结果。这些结果以如下所示的格式显示:

[![Alt text of image](img/fb9eeb2e588bd73631e7789ceeff67dd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yMM4vHGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/locust_exe2.png)

从上面的例子可以看出，有一个登录请求和多个获取页面和提交表单的请求。因为用户数量较少，所以没有故障转移。

现在让我们将请求数量增加到 1000 个用户，孵化率为 500，看看结果。

[![Alt text of image](img/e2e3eba9e05a9ef9a7d3dd3fac27c718.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wNXAKaJ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/locust_user2.png)

[![Alt text of image](img/8eaa06e6b6449ef59c1ce112ea207b8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x46lfqSj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/locust_exe4.png)

正如我们所知，随着用户和请求数量的增加，在这种情况下，一些获取主页和发布表单的请求会失败。对于当前的一组模拟用户，我们得到 7%的失败率。

观察结果:

1.  大部分的失败都是在登录。一些失败源于应用程序阻止在短时间内从同一个帐户多次登录。

2.  获取页面请求的失败率非常低- 3%

3.  Post 请求的失败率较低，不到 2%

我们可以针对不同范围的用户执行多项测试，通过测试结果，可以确定应用程序在多大压力下能够执行。

结果产生以下测试数据:

1.  请求的类型-与要模拟的每个任务相关。

2.  名称-任务/请求的名称。

3.  请求数-任务的请求总数。

4.  失败次数-失败请求的总数。

5.  请求的中值、平均值、最大值和最小值，以毫秒为单位。

6.  内容大小-请求数据的大小。

7.  每秒请求数。

我们可以在 Failures 选项卡中看到失败请求的详细信息，该选项卡可用于确定重复失败的根本原因。

[![Alt text of image](img/96cc50152e7a19ce79a1060a1515af40.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3cxU2CDl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.apcelent.cimg/locust_failures.png)

LocustIO 提供了以表格形式下载结果的选项，但是没有图形或图表形式的现成结果可视化功能。

可以在 http://localhost:8089/stats/requests 上查看 JSON 格式的负载测试结果。这些请求可以用作使用不同工具(如 Tableau、matplotlib 等)的数据可视化的输入。

因此，我们能够以非常简单有效的方式确定不同端点的系统性能。我们可以扩展测试，为更多的端点添加更多的场景，并快速获得答案。

这篇文章最初出现在 [Apcelent 科技博客](https://blog.apcelent.com/Load-Testing-a-Django-Application-using-LocustIO.html)上。