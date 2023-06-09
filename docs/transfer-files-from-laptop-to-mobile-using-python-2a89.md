# 使用 Python 将文件从笔记本电脑传输到手机

> 原文：<https://dev.to/sahilrajput/transfer-files-from-laptop-to-mobile-using-python-2a89>

如果你认为你需要一些奇特的应用软件来把文件从你的笔记本电脑转移到手机上，那你就错了。
**你需要的只是`Python`**

`NOTE: Your laptop and your mobile phone should be on same network`

## 我们开始吧

打开您的终端并执行这个命令。
如果你用的是 **Python3** :

```
python -m http.server 
```

如果您使用的是 **Python2** :

```
python -m SimpleHTTPServer 
```

当您执行这个命令时，它将在您的本地机器上创建一个 HTTP 服务器。
[![screenshot 2018-11-06 at 4 45 57 pm](img/3cf945373db27d5d3ea7c384d9fad94f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--us95dxsP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48061065-c4c6f880-e1e3-11e8-9d35-7aaac69d1adf.png)

在您的移动浏览器中键入`<IP_Address>:8000`以访问您运行上述命令的目录中的文件。

如果你不知道你的 IP 地址，请按照以下步骤操作:
进入**系统偏好- >网络- >你会在那里看到你的 IP 地址**

假设你的 IP 地址是 192.168.0.1，然后打开手机浏览器，输入`192.168.0.1:8000`访问文件。
T12![screenshot 2018-11-06 at 5 25 05 pm](img/73349d2d69e8d697679658a800c9939a.png)T14】