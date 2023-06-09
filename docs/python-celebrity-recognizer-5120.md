# Python 名人识别器

> 原文：<https://dev.to/mitchh_emery/python-celebrity-recognizer-5120>

#### 让我们使用 python 请求库和微软计算机视觉 API 的免费试用版来识别照片中的名人。

这个简短的教程旨在向开发者展示认知服务是多么的容易获得和用户友好。本教程大部分摘自微软的认知服务文档，可以在这里找到。

首先，您需要从[这里获得一个免费的订阅密钥。](https://azure.microsoft.com/en-us/try/cognitive-services/?api=computer-vision)

*首先*，我们需要导入请求库

```
import requests 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要钥匙，和一些基本的网址。我们将使用计算机视觉 API 文档中的这些值。

```
#subscription key key = "your key here"

#Base endpoint and special celebrity endpoint vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0/"
celebrity_analyze_url = vision_base_url + "models/celebrities/analyze" 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个名人的图像。我从流行音乐之王开始。
迈克尔杰克逊

```
image_url = "http://images2.fanpop.com/image/photos/10700000/Close-Up-Large-Photo-michael-jackson-10731676-1267-1333.jpg" 
```

Enter fullscreen mode Exit fullscreen mode

我们需要设置一些基本的 HTTP 变量。这些值是标准值，取自文档。

```
h = {'Ocp-Apim-Subscription-Key':key}
p = {'visualFeatures': 'Categories,Description,Color'}
d = {'url':image_url} 
```

Enter fullscreen mode Exit fullscreen mode

我们最终将请求发送给服务。

```
response = requests.post(celebrity_analyze_url,headers=h,params=p,json=d)
analysis = response.json() 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看我们的结果:

```
result = analysis["result"]["celebrities"][0]["name"]
print(result) 
```

Enter fullscreen mode Exit fullscreen mode

***迈克尔杰克逊***

 *评论该服务可以识别和不能识别哪些名人。

我已经得到了第一个，这个服务不能识别王子...*