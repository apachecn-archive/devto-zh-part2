# 我给优步付了多少钱？

> 原文：<https://dev.to/ajiteshtiwari/how-much-i-have-paid-to-uber-2l7c>

作为一名开发人员，我很高兴看到关于机器学习的会议，展示像聊天机器人或超级棒的图像分析工具这样的生活。

但就我个人而言，在着手处理/分析数据方面，我失败了多次，很多人说这是 ML 的基础。学习数据分析可能很难开始。大多数可用的教程都让我深深陷入复杂的数学公式中，以至于我听了几节课就跟不上了。

这个周末，我决定在没有任何 python 或其他工具知识的情况下做一些基本的事情。我不得不一路寻找解决方案，但总的来说这是一次很好的经历。

所以，我决定从优步的网站上搜集历史资料，试图找到一些答案，比如-

*   我总共向优步支付了多少钱？
*   我花了多少时间在优步游乐设施或等待游乐设施上？
*   我通过选择拼车节省了多少钱？
*   对于各种优步骑行，我每公里支付了多少钱？

虽然数据可以从优步开发者 API 获得。但是为了学习网络抓取的重要概念，我决定走一条更长的路。

还有更多，这取决于你收集了多少数据以及你的调查程度，:D
让我们开始吧

### 第一步——从优步的历史中搜集数据

我们可以从这个[链接](https://riders.uber.com/trips)得到优步的历史。这是一个分页的端点，它有一个乘坐列表。大多数信息都可以在这个页面上找到。

为了使事情更简单，我们有一个可用的插件- [优步数据提取器](https://chrome.google.com/webstore/detail/uber-data-extractor/ckncaneeemafoonmpammikboiakeaggp)。我们只需要打开这个[链接](https://riders.uber.com/trips)并点击插件。在一段时间内，我们将在我们的下载中有一个 excel 表格，其中包含我们需要的大部分信息。

这个插件遍历分页的端点- `https://riders.uber.com/trips?page={page-number}`并获取数据保存在 excel 表中。收集信息后，我们有以下栏目- `trip_id, date, date_time, driver, car_type, city, price, payment_method, start_time, start_address, end_time, end_address.`

但是事情还没有完。还有两个重要的参数需要捕捉- `distance and trip-time.`
这些数据可在每个游乐设备的详细信息页面上获得，可从`https://riders.uber.com/trips/{trip-id}`访问。

幸运的是，我们在从上面的 chrome 插件收集的数据中有 trip-id，使用它我们可以生成一系列我们需要访问的链接。

现在我们可以使用 selenium 和 chrome-web-driver 来自动打开链接和打印所需的字段。由于我在 python 方面没有那么强，所以如果我犯了几个基本错误，请忽略。

```
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import TimeoutException
import time

option = webdriver.ChromeOptions()

browser = webdriver.Chrome(executable_path='/Users/atiwari4/Downloads/chromedriver', chrome_options=option)

browser.get("https://riders.uber.com/trips")
time.sleep(60) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码试图把你带到[https://riders.uber.com/trips](https://riders.uber.com/trips)链接，它反过来要求认证。我们需要登录优步的账户，以便进行后续步骤。
因此，我增加了`time.sleep(60)`。它为我们提供了 60 秒的缓冲时间来帮助我们完成登录过程。

```
for a in arr:
    row = []
    browser.get(a)
    time.sleep(3)
    distance = browser.find_elements_by_xpath("/html/body/div[2]/div/div[2]/div/div/div[2]/div[3]/div/div[1]/div[2]/div[2]/div/div[2]/h5")
    trip_time = browser.find_elements_by_xpath("/html/body/div[2]/div/div[2]/div/div/div[2]/div[3]/div/div[1]/div[2]/div[2]/div/div[3]/h5")
    row.append(a)
    distancex = [x.text for x in distance]
    row.append(distancex)
    trip_timex = [x.text for x in trip_time]
    row.append(trip_timex)
    print row 
```

Enter fullscreen mode Exit fullscreen mode

这里的`arr`是游乐设备详细页面链接的数组。

使用 XPath，我们从页面中挑选所需的元素，并在控制台上打印出来。上述代码可能需要一些时间，这取决于您已经完成的旅行次数。有关使用 selenium 进行网络抓取的更多信息，请参考本文。

现在我的数据表看起来有点像这个。

### 第 2 步-使用谷歌联合实验室分析数据

Google [colabs](https://colab.research.google.com/) 是一个云环境，可以用来运行实时代码。

我们可以使用各种方法在 colabs 中导入工作表。因为我们已经在 google-sheets 上上传了我们的表单，我们可以从那里直接访问它。

首先，我们需要用 colabs 验证我们的 google 帐户。

```
!pip install --upgrade -q gspread
!pip install -U matplotlib

from google.colab import auth
auth.authenticate_user()

import gspread
from oauth2client.client import GoogleCredentials

gc = gspread.authorize(GoogleCredentials.get_application_default()) 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们通过认证，这就是我们如何从 google-sheets
导入表单

```
# Open our new sheet and read some data.
worksheet = gc.open('Uber').sheet1

# get_all_values gives a list of rows.
rows = worksheet.get_all_values()
print(rows)

# Convert to a DataFrame and render.
import pandas as pd
df = pd.DataFrame.from_records(rows)
df.head() 
```

Enter fullscreen mode Exit fullscreen mode

df 代表[数据帧](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)，由熊猫提供。根据那里的文件，它是-

```
Two-dimensional size-mutable, potentially heterogeneous tabular data structure with labeled axes (rows and columns). Arithmetic operations align on both row and column labels. Can be thought of as a dict-like container for Series objects. 
```

Enter fullscreen mode Exit fullscreen mode

`head(n=5)`函数返回数据帧的前 n(默认为 5)行。

[![uber-sheet](img/e1dceaa87abf280f1fcd0c5b41c63257.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e0gJYjfp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/174doh6gqd4dybmgnc92.png)

我们表格中的第一行是错误的，应该作为表格的标题。

```
df.columns = df.iloc[0]
df = df.reindex(df.index.drop(0)) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要定义一个具有唯一值的列。

```
df['trip_id'].is_unique
df = df.set_index('trip_id')
df.head() 
```

Enter fullscreen mode Exit fullscreen mode

[![uber-sheet](img/03204c5acd43982ac479c27a2a4dd981.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vaysMfaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wx4gup99dbg0et4yah73.png)

模式现在看起来更好了。但是数据呢。在我们开始寻找答案之前，我们需要清理它。例如，价格列类似于`₹89.53`。我们需要删除该列的前 3 个字符。

```
df['price'] = df['price'].str.slice(3) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要从 price 列中删除逗号，以便将其数据类型转换为 number。我们还需要日期列的数据类型为日期时间。

```
df['price'] = df['price'].str.replace(',','')
df['price'] = pd.to_numeric(df['price'])
df['date'] = pd.to_datetime(df['date'], format='%m/%d/%y') 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将有很多列为 NaN。尤其是那些被取消的游乐设施，我们需要取消它们。

```
import numpy as np
df['end_time'].replace('', np.nan, inplace=True)
df['end_time'].replace('Dropoff time unknown', np.nan, inplace=True)
df['start_time'].replace('', np.nan, inplace=True)

df.dropna(subset=['end_time'], inplace=True)
df.dropna(subset=['start_time'], inplace=True)
df.dropna(subset=['price'], inplace=True) 
```

Enter fullscreen mode Exit fullscreen mode

最后是十年:D 的问题

# 我给优步付了多少钱？

只要输入这个就能得到你的答案-

```
df['price'].sum() 
```

Enter fullscreen mode Exit fullscreen mode

原来我已经付了 **Rs。26852.03** 到优步(涉及 Uber Eats)。我们还可以分离各种优步服务，看看我们分别为它们支付了多少费用。

为了知道使用 count 的 uber 服务的确切数量，我们需要运行以下代码-

```
print pd.value_counts(df['car_type'].values, sort=True) 
```

Enter fullscreen mode Exit fullscreen mode

这是结果-

[![uber](img/a55ea5263d8519d9acb3c79c98ba68c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w7nOZAkO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/475dvvfkghr3hp4kvpk0.png)

我们还可以使用`matplotlib`库在 colabs 中绘制图形。这是一个时间和价格图表的例子。

[![graph](img/bf94f725248cecc8411faa1878a3e771.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9BAUQNDh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7fcqb1voto4oudkmal71.png)

从这个数据集中可以检索到一千多个答案。请随意尝试新的组合，并在下面的部分发表评论。

点击[此处](https://colab.research.google.com/drive/1NsCpZGsbdXlk-azg7AgMe7gaoy6g7_Gz)查看包含更多推论的 colabs 表。