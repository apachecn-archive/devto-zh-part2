# 用于实际工作搜索 API 的 Python 包装器

> 原文：<https://dev.to/kashaziz/python-wrapper-for-indeed-job-search-api-57of>

# Python Wrapper For each Job Search API

[![Python Wrapper For Indeed Job Search API](img/df1d348e7bedac0e37c292f68b16acdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FvkrfKJx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/76u4lfx01t8dlfkq4pds.jpg)

最近，我在寻找一个 Python 包装器来使用 Indeed API。由于找不到符合我要求的，我写了一个快速简单的 Python 脚本，它使用了 job search API 并将搜索结果存储在 CSV 中。

为了运行脚本并从 Indeed job search API 获取作业，您必须具备:

*   事实上，发布者 API ID 可以从[这里](https://ads.indeed.com/jobroll/xmlfeed)免费获得
*   Python 3.x
*   BeautifulSoup 组和请求库

### 用法:

GitHub 提供了用于 Indeed Job Search API 的 Python wrapper 代码。[从这里下载](https://github.com/kashaziz/indeed-python-wrapper)。

打开 indeedapiwrapper.py，添加以下参数以通过 Indeed Job Search API 获取工作列表:

```
params = {
    'publisher': "",    # publisher ID (Required)
    'q': "",            # Job search query
    'l': "",            # location (city / state)
    'co': "",           # Two letter Country Code
    'sort': "",         # Sort order, date or relevance
    'days': ""          # number of days to fetch jobs, maximum is 7 days
    } 
```

**注:**

*   需要发布者 Id。
*   要搜索职务，请提供查询字符串或位置和国家代码的组合。
*   要返回相关作业，脚本需要查询字符串或位置和国家代码的组合。如果查询字符串不存在，并且位置或国家代码之一缺失，脚本将使用“卡拉奇”和“pk”作为默认的位置和国家代码。

例如，以下搜索参数将搜索巴基斯坦卡拉奇的所有 Python 作业。

```
params = {
    'publisher': "0000000000000000",    # Use valid Id to get results 
    'q': "python",  
    'l': "karachi",  
    'co': "pk",  
    'sort': "date",                # Sort by date
    'days': "3"                    # get jobs for 3 days, including today
    } 
```

### 输出:

作业列表将保存在 CSV 文件“indeedjobs.csv”中，该文件位于脚本所在的同一目录下。

### 资源:

事实上，求职 API 文档可以在[这里](http://opensource.indeedeng.io/api-documentation/docs/job-search/)找到。

实际工作搜索 API 的 Python 包装器代码[从这里下载](https://github.com/kashaziz/indeed-python-wrapper)。