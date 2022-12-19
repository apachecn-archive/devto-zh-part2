# 使用 Tika、Nokogiri 和 Algolia 搜索的索引 PDF

> 原文：<https://dev.to/obahareth/indexing-pdf-for-searching-using-tika-nokogiri-andalgolia-phc>

*本[中岗位](https://stories.algolia.com/indexing-pdf-or-other-file-contents-for-searching-b2499c23568f)的镜子。*

### 弄清楚从哪里开始

这是我写的第一个指南，请多包涵，并提供反馈！

我当时正在做一个项目，这个项目要求我具备一些真正强大的搜索能力，能够支持多种语言，尤其是搜索文件内容(我最初是从 PDF 开始的)。我需要一些有用的东西，但我没有太多的时间。通常在谷歌上快速搜索会找到如何解决这类问题的教程或指南，但这一次我没有任何运气，所以我决定在我有所进展后分享我的经验。

我继续查看 [StackShare](https://stackshare.io/) 并找到了一个关于[搜索即服务](https://stackshare.io/search-as-a-service)的类别，我一直听说弹性搜索的惊人之处，我检查了那里可用的大多数其他工具，并联系了他们的支持团队寻求一些指导。两分钟后，我很惊讶地收到了亚当·苏拉克的回复，他是[Algolia](https://algolia.com)(Medium surgical，甚至 Twitch 使用的搜索服务)的基础设施总监，他帮了我不少忙。他指引我去了阿帕奇 Tika,他们的页面上写着:

> Apache Tika 工具包可以从一千多种不同的文件类型(如 PPT、XLS 和 PDF)中检测和提取元数据和文本。所有这些文件类型都可以通过一个界面进行解析，这使得 Tika 可以用于搜索引擎索引、内容分析、翻译等等。Adam 还建议我把 PDF 分成几个段落，因为这样可以确保搜索到的文本不会太长，而且相关性很高。

* * *

### 提取 PDF 内容

我能够通过[自制](http://brew.sh) :
快速轻松地获得 Tika

```
brew install tika 
```

Enter fullscreen mode Exit fullscreen mode

我将使用从 PDFPlanet 免费获得的 PDF 版本的 Dracula(然后我将除了前 3 页之外的所有内容都剪掉)，我运行:

```
tika -r dracula-shortened.pdf > dracula.html 
```

Enter fullscreen mode Exit fullscreen mode

`-r`选项用于漂亮的打印，Tika 输出 HTML 以便于解析(我使用`>`将 HTML 输出传递到一个名为`dracula.html`的文件中)。让我们看看从中获得的元数据:

```
<head>
<meta name="date" content="2017-01-05T18:08:24Z"/>
<meta name="pdf:PDFVersion" content="1.7"/>
<meta name="xmp:CreatorTool" content="Acrobat PDFMaker 5.0 for Word"/>
<meta name="Keywords" content="free, PDF, ebook, ebooks, Planet PDF, download, classic, classics"/>
<meta name="access_permission:modify_annotations" content="true"/>
<meta name="access_permission:can_print_degraded" content="true"/>
<meta name="subject" content="Free Planet PDF eBooks -- an assortment of some of the most popular classics. Free!"/>
<meta name="dc:creator" content="Bram Stoker"/>
<meta name="dcterms:created" content="2002-09-20T06:10:21Z"/>
<meta name="Last-Modified" content="2017-01-05T18:08:24Z"/>
<meta name="dcterms:modified" content="2017-01-05T18:08:24Z"/>
<meta name="dc:format" content="application/pdf; version=1.7"/>
<meta name="Last-Save-Date" content="2017-01-05T18:08:24Z"/>
<meta name="access_permission:fill_in_form" content="true"/>
<meta name="meta:save-date" content="2017-01-05T18:08:24Z"/>
<meta name="pdf:encrypted" content="false"/>
<meta name="dc:title" content="Dracula"/>
<meta name="modified" content="2017-01-05T18:08:24Z"/>
<meta name="cp:subject" content="Free Planet PDF eBooks -- an assortment of some of the most popular classics. Free!"/>
<meta name="Content-Length" content="48741"/>
<meta name="Content-Type" content="application/pdf"/>
<meta name="X-Parsed-By" content="org.apache.tika.parser.DefaultParser"/>
<meta name="X-Parsed-By" content="org.apache.tika.parser.pdf.PDFParser"/>
<meta name="creator" content="Bram Stoker"/>
<meta name="meta:author" content="Bram Stoker"/>
<meta name="dc:subject" content="free, PDF, ebook, ebooks, Planet PDF, download, classic, classics"/>
<meta name="meta:creation-date" content="2002-09-20T06:10:21Z"/>
<meta name="created" content="Fri Sep 20 09:10:21 AST 2002"/>
<meta name="access_permission:extract_for_accessibility" content="true"/>
<meta name="access_permission:assemble_document" content="true"/>
<meta name="xmpTPg:NPages" content="3"/>
<meta name="Creation-Date" content="2002-09-20T06:10:21Z"/>
<meta name="resourceName" content="Dracula_NT-shortened.pdf"/>
<meta name="access_permission:extract_content" content="true"/>
<meta name="access_permission:can_print" content="true"/>
<meta name="meta:keyword" content="free, PDF, ebook, ebooks, Planet PDF, download, classic, classics"/>
<meta name="Author" content="Bram Stoker"/>
<meta name="producer" content="Acrobat Distiller 5.0.5 (Windows)"/>
<meta name="access_permission:can_modify" content="true"/>
Dracula
</head> 
```

Enter fullscreen mode Exit fullscreen mode

那里有很多有用的数据，你可以使用 Tika 来获取元数据，检测内容语言，以及许多其他强大的选项，但是在我们的例子中，我们对主体更感兴趣，这里有一个主体的片段(PDF 的第二页):

```
<body>

<div class="page">
   <p/>
   <p>Dracula
   </p>
   <p>2 of 684
   </p>
   <p>Chapter 1
   </p>
   <p>Jonathan Harker’s Journal
      3 May. Bistritz.—Left Munich at 8:35 P.M., on 1st
   </p>
   <p>May, arriving at Vienna early next morning; should have
      arrived at 6:46, but train was an hour late. Buda-Pesth
      seems a wonderful place, from the glimpse which I got of
      it from the train and the little I could walk through the
      streets. I feared to go very far from the station, as we had
      arrived late and would start as near the correct time as
      possible.
   </p>
   <p>The impression I had was that we were leaving the
      West and entering the East; the most western of splendid
      bridges over the Danube, which is here of noble width
      and depth, took us among the traditions of Turkish rule.
   </p>
   <p>We left in pretty good time, and came after nightfall to
      Klausenburgh. Here I stopped for the night at the Hotel
      Royale. I had for dinner, or rather supper, a chicken done
      up some way with red pepper, which was very good but
      thirsty. (Mem. get recipe for Mina.) I asked the waiter,
      and he said it was called ‘paprika hendl,’ and that, as it was
      a national dish, I should be able to get it anywhere along
      the Carpathians. </p>
   <p/>
   <div class="annotation">
      <a href="http://www.planetpdf.com/mainpage.asp?eb"/>
   </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这是 PDF 中的第二页以供比较:

[![dracula-2nd-page](../Images/af2b041e6f75d0471d6b4b68c0308ceb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wmvNMfLH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/my0siuy1uhf2wwlsmn0i.png)

从输出中我们可以看到，每个页面都是一个以`page`为类的`div`，然后我们将每个段落作为段落元素`p`的内部文本

这是非常好的格式，非常容易解析。

* * *

### 在 Ruby 中使用 Tika

对我来说，下一步是在 Ruby 中使用 Tika(因为我的堆栈依赖于 Ruby on Rails)。通过快速搜索，我找到了 Yomu，在 Ruby 中使用它变得非常简单

```
require "yomu"
yomu = Yomu.new "dracula-shortened.pdf"
puts yomu.html 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 使用 Nokogiri 解析 HTML

我过去使用过 Nokogiri 来解析 HTML，在这种情况下非常容易。

```
require "nokogiri"
require "yomu"

def invalid_paragraph?(str)
  disallowed_strings = [ "", " ", "\n", " \n" ]
  disallowed_strings.include?(str)
end

def get_pdf_paragraphs(filename)
  yomu = Yomu.new(filename)
  paragraphs = []
  doc = Nokogiri::HTML(yomu.html)
  page = 0

  doc.css('.page').each do |node|
    node.css('p').each do |paragraph|
      paragraph_text = paragraph.inner_text

      next if invalid_paragraph?(paragraph_text)

      paragraphs << { text: paragraph_text, page: page }
    end

    page += 1
  end

  paragraphs
end

paragraphs = get_pdf_paragraphs("dracula-shortened.pdf") 
```

Enter fullscreen mode Exit fullscreen mode

我使用 CSS 选择器遍历以`page`为类的任何内容，然后遍历该页面中的所有段落元素`p`，我只是递增了`page`变量(我这样做是因为知道文本是在哪一页上找到的对我的用例很重要)。我有一堆空的字符串段落，段落中只有一个空格字符或换行符，空格后面跟一个新行，所以我添加了一个简单的函数`invalid_paragraph?`来轻松跳过这些文本(我本来可以编写一个正则表达式，但在这种情况下似乎不值得，特别是因为我想要一个易读的教程)。

让我们来看看`paragraphs`(我在粘贴这个输出之前剪下了一大块 pdf，所以我没有一个真正巨大的例子):

```
[
    {

        :text => "Dracula \nBram Stoker \n",
        :page => 0
    },

    {
        :text => " \nThis eBook is designed and published by Planet PDF. For more free \neBooks visit our Web site at http://www.planetpdf.com/.",
        :page => 0
    },

    {
        :text => "Dracula \n",
        :page => 1
    },

    {
        :text => "2 of 684 \n",
        :page => 1
    },

    {
        :text => "Chapter 1 \n",
        :page => 1
    },

    {
        :text => "Jonathan Harker’s Journal \n3 May. Bistritz.—Left Munich at 8:35 P.M., on 1st \n",
        :page => 1
    },

    {
        :text => "May, arriving at Vienna early next morning; should have \narrived at 6:46, but train was an hour late. Buda-Pesth \nseems a wonderful place, from the glimpse which I got of \nit from the train and the little I could walk through the \nstreets. I feared to go very far from the station, as we had \narrived late and would start as near the correct time as \npossible. \n",
        :page => 1
    },

    {
        :text => "The impression I had was that we were leaving the \nWest and entering the East; the most western of splendid \nbridges over the Danube, which is here of noble width \nand depth, took us among the traditions of Turkish rule. \n",
        :page => 1
    },

    {
        :text => "We left in pretty good time, and came after nightfall to \nKlausenburgh. Here I stopped for the night at the Hotel \nRoyale. I had for dinner, or rather supper, a chicken done \nup some way with red pepper, which was very good but \nthirsty. (Mem. get recipe for Mina.) I asked the waiter, \nand he said it was called ‘paprika hendl,’ and that, as it was \na national dish, I should be able to get it anywhere along \nthe Carpathians. ",
        :page => 1
    },
] 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。多亏了 Tika，我们可以轻松地将 PDF 分成段落，多亏了 Nokogiri，我们可以非常轻松地解析它。

* * *

### 将 PDF 内容导入 Algolia

所以我在试用之后决定使用 Algolia，因为它非常容易使用和配置，而且返回搜索结果的速度也非常快。让我的 PDF 内容可以被搜索就像这样简单:

```
Algolia.init(application_id: 'xxxx', api_key: 'xxxx')
index = Algolia::Index.new("books")
index.add_objects(paragraphs)
index.set_settings({ "searchableAttributes" => ["text"] }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 期末备注

显然，您可以向该示例中添加更多内容(也许还可以将文件名和 PDF 作者/标题作为记录，或者添加链接到您的系统的图书 id 或其他内容)，但这是一个很好的简单示例。我想感谢所有为我用过的工具工作的人，因为他们是了不起的、不可思议的省时者。

以下是 GitHub 要点示例的完整代码: