# 关于净化这个 Python RSS 抓取代码的想法？

> 原文：<https://dev.to/katiekodes/thoughts-about-sanitizing-this-python-code-h6p>

我编写了一个快速的 Python 脚本来生成 HTML，以法语和英语并排显示双语科技漫画“CommitStrip ”,这样我就可以更好地比较两个版本之间的措辞。

它帮助我更有效地学习习语。

我在想，我会把它作为一个单独的页面，放在 Heroku 这样的主机服务上，放在一个秘密的 URL 上，并不时地访问它。(所以，最终，我会有更多的 Python，以确保有一个服务器监听返回 HTML 的请求。)

问题是...我不是一个有经验的 web 开发人员。

我可能想要消毒什么，什么是过度消毒？

*   例如，让我的浏览器访问这些图片，似乎并不比去他们的网站做同样的事情更危险。但我想我会想办法确保“图片”只是一个简单的 URL 格式，就像他们通常遵循的格式一样？这有必要吗？
*   我是否应该担心在“响应”到达我的 Python 模块(如 ElementTree)之前以任何方式净化它？(当 CommitStrip 网站的其他部分没有损害所有访问者时，commit strip 的 RSS feed 是否有被破坏的特殊危险？)
*   等等。

我有一个总的感觉，你不应该只是“从陌生人那里拿东西，然后在浏览器上显示出来”，但是我不确定当涉及到“抓取网站并重新显示它们的内容”时会发生什么。

感谢任何提示！

> (旧代码已删除)

## 更新代码，成功(但不确定安全程度)部署到 Heroku:

```
import os
import requests
from bs4 import BeautifulSoup
import bleach
from flask import Flask, render_template, Markup

# Initialize the Flask application app = Flask(__name__)

contentstring = '{http://purl.org/rss/1.0/modules/content/}'
rf = requests.get('https://www.commitstrip.com/fr/feed/')
re = requests.get('https://www.commitstrip.com/en/feed/')

fcl = bleach.clean(rf.text if rf.status_code==200 else '', tags=['rss','item','img','guid','title'], attributes={'img':['src','class']}, strip=True)
ecl = bleach.clean(re.text if re.status_code==200 else '', tags=['rss','item','img','guid','title'], attributes={'img':['src','class']}, strip=True)

comicsDict = {}
for (lang, postslist) in [('fr', BeautifulSoup(fcl, 'html.parser').find_all('item')), ('en', BeautifulSoup(ecl, 'html.parser').find_all('item'))]:
    for post in postslist:
        masterid = None
        masterid = post.find('guid').get_text().split('?p=')[1]
        if masterid not in comicsDict:
            comicsDict[masterid] = {}
        comicsDict[masterid][lang+'_'+'title']=post.find('title').get_text()
        comicsDict[masterid][lang+'_'+'imgurl']=post.find('img', {'class':'alignnone'})['src']

htmlstring = Markup(
        '<html><head><meta charset="UTF-8" />CommitStrip Feed Bilingue</head><body>' +
        ''.join(['<div class="comic" style="width:100%;"><div class="fr" style="float: left; width: 50%;"><img src="' + v['fr_imgurl'] + '" style="width:100%;"><figcaption>' + v['fr_title'] + '</figcaption></div> <div class="en" style="float: left; width: 50%;"><img src="' + v['en_imgurl'] + '" style="width:100%;"><figcaption>' + v['en_title'] + '</figcaption></div><br style="clear: both;" /></div>' for k,v in comicsDict.items()])
        + '</body></html>'
    )

# Define content for the home page of our app @app.route('/')
def index():
    return render_template('index.html', 
                                       thepage=htmlstring
                                       )

# Start the server
#'''
# This seems to work well when live on Heroku (localhost, my firewall yells at me) if __name__ == '__main__':
    port = int(os.environ.get("PORT", 5000))
    app.run(host="0.0.0.0", port=port)
#''' 
'''
# This seems to work well when testing on localhost
if __name__ == '__main__':
    app.run()
''' 
```

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    Commitstrip Bilingue
  </head>
  <body>
    <div class="wholepage">
        {{ thepage }}
    </div>
  </body>
</html> 
```