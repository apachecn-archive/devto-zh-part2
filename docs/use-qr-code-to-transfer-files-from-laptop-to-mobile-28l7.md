# 用 Python 生成二维码，将文件从笔记本电脑传输到手机

> 原文：<https://dev.to/sahilrajput/use-qr-code-to-transfer-files-from-laptop-to-mobile-28l7>

在我之前的文章中，我展示了如何通过一个简单的命令将文件从笔记本电脑转移到手机上。

灵感来自下面的评论。我决定创建一个 Python 程序，它将生成一个二维码，使用这个二维码，你可以在手机上访问你的笔记本电脑文件。

[![defman profile image](img/e53b5295c644d26210e008dddd7a2e2f.png) ](/defman) [ Sergey Kislyakov ](/defman) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/_defman) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/Defman21) [<time datetime="2018-11-06T13:20:05Z">Nov 6 '18</time>](/defman/comment/6jdb)

我看到一个应用程序可以生成二维码，通过局域网访问文件。名字不记得了，不过我觉得自己写也没那么难。这比在浏览器中输入 192.168 .随便什么然后找到文件要简单得多。

```
#Import libraries
import socket 
import http.server
import socketserver 
import cv2  
import pyqrcode
from pyqrcode import QRCode
from PIL import Image 
```

获取 IP 地址

```
hostname = socket.gethostname()    
IP = socket.gethostbyname(hostname)      
print("Your Computer IP Address is:" + IP) 
```

`url1`是一个变量，它将连接 IP 地址和端口号以形成一个字符串，该字符串将被转换成 QR 码，然后`http.server`将开始运行。

```
url1 = IP + ":" + "8000"

#Generate QR Code
url = pyqrcode.create(url1)
url.png("myqr.png",scale=8)
img = cv2.imread("myqr.png")
#print(type(img))
cv2.imwrite('myqr.png',img)
im = Image.open('myqr.png')
im.show()

#Start http server
Handler = http.server.SimpleHTTPRequestHandler
with socketserver.TCPServer(("", 8000), Handler) as httpd:
    print("Running your port")
    httpd.serve_forever() 
```

现在，用名称`test.py`保存文件并运行。

```
python test.py 
```

[![screenshot 2018-11-06 at 11 34 52 pm](img/15d739e856ae083a8e526eec3ec977eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5PwacIlm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48084390-95cd7880-e21d-11e8-9362-6c6f8a0127e8.png) 
运行后，二维码会弹出到你的屏幕上，服务器开始运行。
[![screenshot 2018-11-06 at 11 34 59 pm](img/d508f86b0cdd582038550fe4e64cabff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XtzKlk40--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48084394-97973c00-e21d-11e8-9d96-ac778647e88a.png) 
要访问您的文件，请扫描二维码并将该链接粘贴到您的手机浏览器中。
[![screenshot 2018-11-06 at 11 36 25 pm](img/cbaee401c497da48f651f02dcb67d211.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V7jKo6t7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/48084397-98c86900-e21d-11e8-9589-97542b3e9c54.png)

`NOTE: Your laptop and your mobile should be to same network`

代码: [GitHub](https://github.com/sahil-rajput/FileTransfer/blob/master/test.py)