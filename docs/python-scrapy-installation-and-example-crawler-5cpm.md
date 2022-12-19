# Python Scrapy 安装和示例爬虫

> 原文：<https://dev.to/hakan/python-scrapy-installation-and-example-crawler-5cpm>

Scrapy 是一个用 Python 编写的开源框架，允许你从 html 和 xml 等结构化内容中提取数据。它能够特别快地在网站上抓取和爬行。首先你要安装 python 包管理器，pip。

使用 pip 安装:

```
pip install scrapy 
```

开始新项目:

```
scrapy startproject tutorial 
```

当一个 scrapy 项目被创建时，一个文件/目录结构将被创建如下。

```
tutorial/
    scrapy.cfg            # deploy configuration file

    tutorial/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items definition file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py 
```

蜘蛛示例:

```
import scrapy

class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
        'http://quotes.toscrape.com/page/2/',
    ]

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').extract_first(),
                'author': quote.css('small.author::text').extract_first(),
                'tags': quote.css('div.tags a.tag::text').extract(),
            } 
```

Scrapy 蜘蛛可以扫描和提取一个或多个地址的数据。使用 Scrapy 选择器，可以选择和过滤所需的字段。选择器中的 xpath Scrapy 支持 scrapy。

对于爬网:

```
scrapy crawl quotes 
```

要将输出写入 json 文件:

```
scrapy crawl quotes -o qutoes.json 
```