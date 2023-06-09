# 使用 PDFKit 和 WkHTMLtoPDF 无法显示的图像

> 原文：<https://dev.to/nodefiend/images-not-showing-up-using-pdfkit-and-wkhtmltopdf-44e8>

照片认证:[布里吉塔·施奈特](https://unsplash.com/@brisch27)

#### 假设:

*   您可以在应用程序上使用 WkHTMLtoPDF 来生成 PDF 文档
*   您正在将图像(通过 URL)传送到预先生成的 HTML 模板

## 问题:

*   WkhtmltoPFf 在生成的 PDF 文档上打开资源的方式在很大程度上是隐蔽的。可以说是一个黑匣子。
*   如果图像没有存储在本地服务器上，这可能会导致图像不会出现在您生成的 PDF 文档中。

## 解:

1.  手动下载图像
2.  将图像转换为 base64 字符串
3.  手动插入 HTML 模板。

### 我们的设置:

*   我们正在使用 ruby on rails 后端，加载 PDF 包 gem
*   我们的前端正在上传图像，并将它们发送到我们的 RAILS API 以生成 HTML 文档
*   我们的控制器使用上传的图像来生成 HTML 文档，并将其输入 PDF 包

控制器中的助手:
`doc_helper.rb`

```
def generate_html_template(image_url)

    "<!DOCTYPE html>
    <html>
        <head>
        </head>
        <body>
        ... some html template
        <img src='#{image_url}'/>
        </body>
    </html>
    "
end 
```

Enter fullscreen mode Exit fullscreen mode

这个助手创建了一个临时的 HTML 文档，我可以把它放入 PDF 包中

做这件事有许多不同方法，但为了说明一点，我就这样做。

**我们的问题是，我们发送到这里的图像 url 来自受保护的 AWS s3 存储桶，此外，它还有一个云前端签名，这给我们带来了各种各样的问题。WkHTMLtoPDF 在解析 html** 时无法呈现图像

### 我们是如何解决的:

`application.rb`

```
require 'rails/all'
require 'base64' 
```

Enter fullscreen mode Exit fullscreen mode

`doc_helper.rb`

```
def download_image(url)
    return 'data:image/png;base64,' + Base64.encode64(open(url) { |io| io.read })
end

def generate_html_template(image_url)

    "<!DOCTYPE html>
    <html>
        <head>
        </head>
        <body>
        ... some html template
        <img src='#{download_image(image_url)}'/>
        </body>
    </html>
    "
end

# credit: https://stackoverflow.com/questions/1547008/how-to-encode-media-in-base64-given-url-in-ruby 
```

Enter fullscreen mode Exit fullscreen mode

在同一行中，我们使用`open()`读取 rails 文件中的文件，然后我们使用 Base64 模块进行转换。

在这之后，我们只需将它注入到模板中，这样 WKHTMLtoPDF 就不必从源文件中打开文件，它只是将它作为 Base64 字符串读取！

### 如果您在实施此修复时有任何问题，请告诉我！