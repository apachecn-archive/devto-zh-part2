# 验证、格式化和美化 JSON 字符串的 Python 脚本

> 原文：<https://dev.to/anuragrana/python-script-to-validate-format-and-beautify-json-string-1a4l>

[![](../Images/a4feb1eba74013e3e64c84cc0ec8d32b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JsBfuppf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AGbIQPCAmgZabjDkMhH58Gg.jpeg)

根据 JSON 官方网站，JSON 是一种轻量级数据交换格式。对人类来说，读和写很容易。机器很容易解析生成。它基于 JavaScript 编程语言的子集，标准 ECMA-262 第三版—1999 年 12 月。

在这篇小文章中，我们将看到如何使用 python 来验证和格式化 [JSON](http://www.json.org/) 字符串。

**格式 JSON 字符串:**

```
import json

json_string = '{"first_name": "Anurag", "last_name":"Rana", "key3": {"key1":"key2"}}'

try:
    parsed_json = json.loads(json_string)
    print(json.dumps(parsed_json, indent = 4,sort_keys=False))
except Exception as e:
    print(repr(e)) 
```

**输出:**

```
rana@Brahma: scripts$ python3 jsonParse.py 
{
    "first_name": "Anurag",
    "last_name": "Rana",
    "key3": {
       "key1": "key2"
    }
}
rana@Brahma: scripts$ 
```

参数`indent`决定用于缩进的空格数。参数`sort_keys`决定格式化 JSON 中的键是否应该排序。默认值为`False`。

**格式化 JSON 文件:**

我们可以使用相同的代码来格式化和验证 JSON 文件。将包含以下内容的. json 文件放在当前工作目录中。

```
{"a": "b","c": "d","e": ["f","g","h"]} 
```

现在使用这段代码格式化 json 文件。

```
import json

json_string = None

with open("json_file.json") as f:
    json_string = f.read()
try:
    parsed_json = json.loads(json_string)
    formatted_json = json.dumps(parsed_json, indent = 4,sort_keys=True)
    with open("json_file.json","w") as f:
    f.write(formatted_json)
except Exception as e:
    print(repr(e)) 
```

**JSON 验证错误:**

JSON 字符串中的任何错误都将被报告，并给出字符串中的确切位置。

例如，下面的 JSON 字符串中缺少了一个逗号。

```
import json

json_string = '{"first_name": "Anurag" "last_name":"Rana", "key3": {"key1":"key2"}}'

try:
    parsed_json = json.loads(json_string)
    print(json.dumps(parsed_json, indent = 4,sort_keys=True))
except Exception as e:
    print(repr(e)) 
```

**输出:**

```
JSONDecodeError("Expecting ',' delimiter: line 1 column 25 (char 24)",) 
```

**奖金:**

您可以在命令终端直接输出小的 JSON 字符串，而无需使用 python 脚本文件。在代码行下面使用。

```
echo '{"first_name": "Anurag", "last_name": "rana"}' | python -m json.tool 
```

**输出:**

```
{
 "first_name": "Anurag",
 "last_name": "rana"
} 
```

你可以在你的浏览器中添加[这个 crome 应用](https://chrome.google.com/webstore/detail/json-borne-json-validator/jdlfgjpngcbjpdkbobhijcpfaainabai)来验证和美化 JSON 字符串。

*最初发表于*[*【www.pythoncircle.com】*](https://www.pythoncircle.com/post/576/python-script-3-validate-format-and-beautify-json-string-using-python/)*。*

更多来自[https://www.pythoncircle.com](https://www.pythoncircle.com)

*   [**如何在 Pythonanywhere 上免费托管 Django App**](https://www.pythoncircle.com/post/18/how-to-host-django-app-on-pythonanywhere-for-free/)
*   [**Python 中链表、集合和元组的区别以及更多这样的比较**](https://www.pythoncircle.com/post/249/difference-between-list-set-and-tuples-in-python-and-more-such-comparisons/)
*   [**如何用 Python 创建完全自动化的电报通道**](https://www.pythoncircle.com/post/265/how-to-create-completely-automated-telegram-channel-with-python/)
*   [**弹性搜索与基巴纳和姜戈**T3】](https://www.pythoncircle.com/post/291/elastic-search-with-kibana-and-django/)