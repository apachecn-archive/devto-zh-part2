# 尝试 Zalenium 在可伸缩容器上运行硒测试

> 原文：<https://dev.to/bufferings/tried-zalenium-to-run-selenium-tests-on-scalable-containers-2n7c>

我的朋友是一位出色的测试工程师，他昨天提到了 Zalenium，我对此很感兴趣。所以我试了一下。

## [马鞭草？](#zalenium)

[https://zalando.github.io/zalenium/](https://zalando.github.io/zalenium/)

经过几个小时的试用，我开始喜欢它，因为

*   1)在我的本地机器上尝试非常容易，
*   2)它动态启动容器来运行 Selenium 测试，
*   3)并提供测试的视频记录

## 1)在我的本地机器上尝试非常容易

### 启动 Zaleinum

```
# Pull 2 docker images
docker pull elgalu/selenium
docker pull dosel/zalenium

# Run it!
docker run --rm -ti --name zalenium -p 4444:4444 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /tmp/videos:/home/seluser/videos \
  --privileged dosel/zalenium start 
```

Enter fullscreen mode Exit fullscreen mode

(在挂载 docker.sock 时，您可能需要小心，尽管这不是这里的主要话题。)

然后你可以进入实时预览页面[http://localhost:4444/grid/admin/Live](http://localhost:4444/grid/admin/live)，发现 2 台机器正在运行。

[![Live Preview](../Images/3d3a184269ddfd3eada4e7d0f17fdd8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4I05sgR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yzahzecr9idpsdsuxic9.png)

### 运行测试

现在，您可以对 Zalenium 进行硒测试了。

我把我喜欢的 Geb 脚本放在这里，但是你可以用你喜欢的。

[http://www.gebish.org/](http://www.gebish.org/)

**HelloZelenium.groovy**

```
Browser.drive {
  go "http://gebish.org"

  assert title == "Geb - Very Groovy Browser Automation"

  $("div.menu a.manuals").click()
  waitFor { !$("#manuals-menu").hasClass("animating") }

  $("#manuals-menu a")[0].click()

  assert title.startsWith("The Book Of Geb")
} 
```

Enter fullscreen mode Exit fullscreen mode

这是来自 Geb 文件。我跳过了对这个脚本的解释，因为我认为您可以很容易地想象它是做什么的。

**gebconfig . groovy**T2】

```
driver = {
  def capabilities = new DesiredCapabilities();
  capabilities.setCapability(CapabilityType.BROWSER_NAME, BrowserType.CHROME);
  capabilities.setCapability(CapabilityType.PLATFORM_NAME, Platform.LINUX);

  def remoteWebDriverUrl = new URL("http://localhost:4444/wd/hub")
  new RemoteWebDriver(remoteWebDriverUrl, capabilities)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用 GebConfig 设置配置。我们使用 RemoteWebDriver 和 Zelenium Hub URL。

包括`import`的完整源代码在这里:[https://gist . github . com/buffer ings/a 8980 ea 515 a 893 e 21 a 3a 95955 ace 5 DC 9](https://gist.github.com/bufferings/a8980ea515a893e21a3a95955ace5dc9)

运行测试

```
groovy HelloZalenium 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以看到测试在实时预览页面上执行。

[![Live Preview](../Images/aa3888ae1fa26e6c6c1d64ffbd21d344.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--igTrEtBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3sjj8eqddbfqp25q7ts5.png)

如此简单！！

## 2)它动态启动容器来运行 Selenium 测试，

然后我想同时进行多项测试。

```
for i in {1..8};do groovy HelloZalenium & done 
```

Enter fullscreen mode Exit fullscreen mode

然后，8 台机器开始测试！测试结束后，它会自动关闭容器。

[![Live Preview3](../Images/e56980983feda7e5eb41728ddfc2c307.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DBkkDSM---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4mnjn4erdt7wsduq7tw7.png)

## 3)并提供测试的视频记录

默认情况下，所有的测试都会被记录下来。我们可以在[上查看](http://localhost:4444/dashboard/#)

[![Recorded Video](../Images/bc68668a7f298853966e306466628d65.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7-NE4ZNn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/odim68hse2g8fxvtfy14.png)

## 配置选项

此外，我们可以通过配置来要求 Zelenium 做一些事情，例如:

```
driver = {
  def capabilities = new DesiredCapabilities();
  capabilities.setCapability(CapabilityType.BROWSER_NAME, BrowserType.FIREFOX);
  capabilities.setCapability(CapabilityType.PLATFORM_NAME, Platform.LINUX);
  capabilities.setCapability("screenResolution", "1280x720");
  capabilities.setCapability("tz", "Asia/Tokyo");

  def remoteWebDriverUrl = new URL("http://localhost:4444/wd/hub")
  new RemoteWebDriver(remoteWebDriverUrl, capabilities)
} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我们可以看到 FireFox 使用了 1280x720 和 Asiz/东京时区。

[![Live Preview4](../Images/7306cf5a97b2d51b32f0961bc82fe62c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T5Uzowky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7krp9rt8w3sjkh6wy1b7.png)

## 还有更！

我还没试过，但是 Zalenium 有更多好的特性:

*   SauceLabs/browser stack/testing bot 集成
*   支持 Kubernetes 环境
*   等等...

## 结论

Zelenium 使用 Docker 运行 Selenium Grid 是如此方便和容易。我想继续尝试和学习它！