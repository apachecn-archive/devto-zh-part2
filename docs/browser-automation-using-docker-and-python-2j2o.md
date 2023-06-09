# 使用 docker 和 python 实现浏览器自动化

> 原文：<https://dev.to/oivoodoo/browser-automation-using-docker-and-python-2j2o>

几周前，我在做一份关于客户游戏的新报告。提供活动报告的平台没有公共 API 来根据请求生成活动报告，也没有任何类型的开发者密钥来访问。

但是可以通过使用他们的仪表板来请求这样的报告。我知道依靠用户界面下载这样的报告有点奇怪，但这是获取客户宝贵数据的唯一方法。

让我们定义这个想法的需求:

*   应该是独立的 python 脚本，以便于执行和与现有的 ETL 库集成
*   除了 docker 包之外，不应该在服务器上要求额外的软件(这非常灵活)

现在我们准备尝试并构建一些可运行的东西。在这篇文章中，我将使用特定的库来访问 docker 进程，因为 CentOS 中安装了特定版本的包(在我的例子中)。

我的要求. txt:

```
docker==2.1.0
splinter==0.7.7
timeout-decorator==0.3.3 
```

Enter fullscreen mode Exit fullscreen mode

splinter 是一个很好的库，它将浏览器驱动程序包装成自动化页面上的任何东西。

让我们定义运行`Google Chrome`容器的类，稍后我们将使用 before 通过`splinter`库访问页面。

```
class _ChromeContainer:
    '''
    _ChromeContainer should handle run of chrome docker container
    on background.

    Requires to have docker service on machine to pull images
    and run images.
    '''
    def __init__(self):
        self.__image_name = "selenium/standalone-chrome:3.10.0"
        self.__client = docker.from_env()

    def run(self):
        '''
        Startup docker container with chromedriver, waiting for running state
        '''
        client = self.__client

        self.container = client.containers.run(self.__image_name,
                                               detach=True,
                                               ports={'4444/tcp': None})

        @timeout_decorator.timeout(120)
        def waiting_up(client: docker.client.DockerClient, container):
            while True:
                container.reload()
                if container.status == "running":
                    break
                time.sleep(1)

        waiting_up(client, self.container)

    def quit(self):
        '''
        kills and deletes named container
        '''
        self.container.kill()

    @property
    def public_port(self):
        container = self.__chrome_container.container
        return container.attrs["NetworkSettings"]["Ports"]["4444/tcp"][0]["HostPort"] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备使用`splinter`和 ahd `_ChromeContainer`来自动化您的任务。

```
import timeout_decorator
import docker

from splinter import Browser

class Worker:
    def __init__(self):
        self.__chrome_container = _ChromeContainer()

    def process(self):
        self.__chrome_container.run()

        self.__web_client = Browser('remote',
                                    url="http://127.0.0.1:{}/wd/hub".format(self.__chrome_container.public_port),
                                    browser='chrome')

        # Example for login request:
        try:
            self.__login()
        finally:
            self.__web_client.quit()
            self.__chrome_container.quit()

    def __login(self):
        self.__web_client.visit("http://www.example.com/login")
        self.__web_client.fill('developer_session[email]', 'EXAMPLE_USERNAME')
        self.__web_client.fill('developer_session[password]', 'EXAMPLE_PASSWORD')
        button = self.__web_client.find_by_id('developer_session_submit')
        button.click() 
```

Enter fullscreen mode Exit fullscreen mode

这是一个例子，可以通过类似于`Worker`类中的`__login`的步骤来扩展。

感谢您的阅读！:)