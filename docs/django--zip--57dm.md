# 如何在 Django 上实时创建 zip 并返回响应

> 原文：<https://dev.to/melpon/django--zip--57dm>

有时会想用用户指定的任意组合，将多个文件集中在 zip 上下载。
因为按组合的数量保存 zip 也很困难，所以希望有一边实时生成一边返回的结构。
因此，我们制作了在 Django 上实时制作 zip 的同时回复响应的功能。

要在 Django 上实时返回响应，请使用[`StreamingHttpReponse`](https://docs.djangoproject.com/en/2.0/ref/request-response/#streaminghttpresponse-objects)。
如下。

```
from django.http import StreamingHttpResponse
from django.views import View

class DownloadView(View):
    def get(self, request):
        images = ['foo.jpg', 'bar.jpg', 'baz.jpg']
        response = StreamingHttpResponse(download_zip_streaming(images), content_type="application/zip")
        response['Content-Disposition'] = f'attachment; filename="download.zip"'
        return response 
```

Enter fullscreen mode Exit fullscreen mode

`images`是固定的文件名，但本来请包含在请求中，或者从 DB 中捡起来。
`DownloadView`一边将这些文件压缩为 zip 一边返回响应。

`download_zip_streaming(images)`成为 Python 的生成器，是实时地一个接一个地生成 zip 的字节数组的代码。
据此，可以一边 zip 实时生成一边返回响应。

`download_zip_streaming`函数如下。

```
class ZipBuffer(object):
    def __init__(self):
        self._buf = b''

    def write(self, buf):
        self._buf += buf
        return len(buf)

    def flush(self):
        pass

    def consume(self):
        # 溜めておいたバッファを全て消費する
        result = self._buf
        self._buf = b''
        return result

# images の画像を、ストリーミングで zip に圧縮する def download_zip_streaming(images):
    session = boto3.Session(region_name=settings.AWS_REGION)
    client = session.client('s3')

    zb = ZipBuffer()
    with zipfile.ZipFile(zb, mode='w') as zf:
        for image in images:
            # 画像を buf にダウンロード
            buf = io.BytesIO()
            client.download_fileobj(settings.S3_BUCKET, f'images/{image}', buf)
            buf.seek(0)

            zf.writestr(image, buf.read())
            yield zb.consume()

    yield zb.consume() 
```

Enter fullscreen mode Exit fullscreen mode

`download_zip_streaming`函数从 S3 检索到文件，并使用`zipfile`对其进行压缩。
从 S3 那里捡回来的部分什么都可以，不用从文件里读或者用程序做，随心所欲就可以了。

[`zipfile.ZipFile`](https://docs.python.jp/3/library/zipfile.html#zipfile.ZipFile)是制作 zip 的模块，一般指定文件名并输出到文件中，但是还接受其他的 file-like 对象。

因此，代替让文件写入，而是让其输出到名为`ZipBuffer`的自制的 file-like 对象，用`yield`返回其输出的 zip 数据，从而实时下载 zip。

这样看起来可以很好地工作。
实际上，在下载 zip 数据之前都可以正常工作。
但是，如果尝试用 Mac 的归档实用程序展开，会显示以下错误。

[![展開エラー](img/2d375c6131c4bd716cf3e75f982e0be3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T9uLttrl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kdc8atqb4m25v8v3h99g.png)

`unzip`命令可以正常工作，但在 Mac 的归档实用程序中无法正常工作。
调查后发现，Mac 似乎不支持 zip 的数据描述符。

zip 的文件单元标题(本地文件标题)中，压缩后的数据之前就存在压缩后的文件大小和 CRC 等非压缩后未知的信息。
为了应对这种情况，在 zip 中，压缩后的数据后面可以有 12 字节的数据描述符区域。
通过在这里写入压缩完毕的文件大小和 CRC 等信息，可以进行 zip 的流媒体压缩。

但是，由于 Mac 不支持该数据描述符，所以无法进行流媒体压缩。
该怎么办，我看着`zipfile`的源代码和 zip 格式的规格思考了一会儿，发现即使不是全体都可以查找，只要倒回一个文件就可以了。

从`zipfile`的实现来看，如果文件- like 对象可以查找，则只压缩并写入一个文件后，在查找到该文件的本地文件头的基础上写入文件大小等。
也就是说，只需创建一个文件的缓冲区的 file-like 对象即可。

```
# consume するまではバッファに溜め続けて、
# その範囲内であれば seek 可能な file-like オブジェクト class ZipBuffer(object):
    def __init__(self):
        self._buf = b''
        self._pos = 0
        self._minpos = 0

    @property
    def _maxpos(self):
        return self._minpos + len(self._buf)

    def tell(self):
        return self._pos

    def seek(self, pos):
        # 既に消費されたバッファに seek しようとした
        if pos < self._minpos:
            raise RuntimeError(f'pos error: pos={pos}, minpos={self._minpos}')
        # 最大位置を超えたらバッファを b'\0' で埋める
        if pos > self._maxpos:
            self._buf += b'\0' * (pos - self._maxpos)
        self._pos = pos

    def write(self, buf):
        written = len(buf)
        # 現在位置がバッファの途中だった場合は上書きする
        if self._pos < self._maxpos:
            n = min(self._maxpos - self._pos, len(buf))
            x = self._pos - self._minpos
            self._buf = self._buf[:x] + buf[:n] + self._buf[x + n:]
            self._pos += n
            buf = buf[n:]

        # 最大位置を超えた分は単に追加する
        self._buf += buf
        self._pos += len(buf)
        return written

    def flush(self):
        pass

    def consume(self):
        # 溜めておいたバッファを全て消費する
        # 以降はこの位置にはシーク不可能になる
        self._minpos += len(self._buf)
        result = self._buf
        self._buf = b''
        return result

# download_zip_streaming はさっきの実装と同じ def download_zip_streaming(images):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，下载的 zip 可以安全地在 Mac 上的归档实用程序中打开。
需要实时返回 zip 时，如果安装这样的功能，Mac 也可以返回可打开的 zip。

但是，如果 1 个文件很大的话就不能使用这个方法，所以在那种情况下就放弃吧。