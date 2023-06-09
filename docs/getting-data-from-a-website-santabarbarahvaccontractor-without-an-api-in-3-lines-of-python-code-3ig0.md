# 用 3 行 Python 代码从一个没有 API 的网站获取数据

> 原文：<https://dev.to/aplusrefrig/getting-data-from-a-website-santabarbarahvaccontractor-without-an-api-in-3-lines-of-python-code-3ig0>

我们讨论如何在没有合适的 API 的情况下，自动完成从网站获取数据的过程，从而节省时间和精力。

假设您在搜索项目所需的数据时，偶然发现了这样一个网页[https://www . santabarrahvaccontractor . com:](https://www.santabarbarahvaccontractor.com:)

这些是你的项目所需的所有数据。

但是，如果您需要的数据在一个不提供接收这些数据的 API 的站点上，该怎么办呢？当然，您可以花几个小时编写一个处理程序来接收这些数据，并将其转换成您的应用程序所需的格式。

但是有一个更简单的解决方案 Pandas 库及其内置函数 read_html()，该函数旨在从 html 页面中检索数据。

*导入熊猫为 PD*
*tables = PD . read _ html(【https://www.santabarbarahvaccontractor.com/"】)*
* print(tables[0])*

是的，一切都那么简单。Pandas 找到页面上的 html 表，并将其作为新的 DataFrame 对象返回。

现在让我们试着告诉 Pandas，表的第一行(确切地说是零行)包含列标题，并让它从包含日期和时间的列中的行生成一个 datetime-object。

*导入熊猫为 pd*
*calls_df，= pd.read_html("URL "，header=0，parse _ dates =[" Call Date "])*
*print(calls _ df)*

现在你知道了如何使用 Python 和 Pandas，你可以毫不费力地从几乎任何站点快速获取数据