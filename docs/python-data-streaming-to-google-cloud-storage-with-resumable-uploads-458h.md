# Python 数据流到 Google 云存储，可继续上传

> 原文：<https://dev.to/sethmlarson/python-data-streaming-to-google-cloud-storage-with-resumable-uploads-458h>

几天前，我花了一个下午的大部分时间来实现从 Python 运行时到 Google 云存储(GCS)的高效内存数据流。

在这个过程中有几个障碍，我想创建文档，我希望在处理这个问题时可以找到这些文档。

本文使用的是 Python 3.6.4，但是也可以适用于其他 Python 版本。

## `google-cloud`模块中的 GCS 支持

[`google-cloud`](https://googlecloudplatform.github.io/google-cloud-python/) 包是一个巨大的模块集合，可以用来与所有的谷歌云平台服务接口，所以这是一个很好的起点。

`python -m pip install -U google-cloud`

在`google-cloud`包中有一个名为 [`google.cloud.storage`](https://googlecloudplatform.github.io/google-cloud-python/latest/storage/client.html) 的模块，它处理 GCS 的所有事情。

我在本地下载并设置了我的`GOOGLE_APPLICATION_CREDENTIALS`,并打开了一个 Python 控制台来测试一些功能。我能够快速连接到 GCS，创建一个 Bucket，创建一个 Blob，然后[将二进制数据上传到 Blob](https://googlecloudplatform.github.io/google-cloud-python/latest/storage/blobs.html#google.cloud.storage.blob.Blob.upload_from_string) 。

```
from google.cloud import storage

client = storage.Client()
bucket = client.create_bucket('test-bucket')
blob = client.blob('test-blob')

blob.upload_from_string(
    data=b'x' * 1024,
    content_type='application/octet-stream',
    client=client
) 
```

Enter fullscreen mode Exit fullscreen mode

我立即注意到的一件事是，为了构建 [Armonaut](https://github.com/Armonaut) ,我的用例会逐步将输出流式传输到 GCS，而不会将输出保存到计算实例的文件系统中。必须有一种方法来传输数据，而不是一次性全部上传。

## 可恢复上传救援！

我做的最初研究发现[可恢复上传](https://cloud.google.com/storage/docs/json_api/v1/how-tos/resumable-upload)是谷歌云存储的一个选项。根据他们的描述，他们有以下用例:

*   您正在上传一个大文件。
*   网络故障的几率很高。
*   上传开始的时候不知道文件的大小。

原因#1 和#3 都适用于我的用例，所以我开始进一步调查。

我在`google-cloud`文档中搜索了关于产生了 [`Blob.create_resumable_upload_session()`](https://googlecloudplatform.github.io/google-cloud-python/latest/storage/blobs.html#google.cloud.storage.blob.Blob.create_resumable_upload_session) 方法的可恢复上传的描述。此方法启动可恢复的上载并返回一个 URL。

## 可恢复媒体包

为了成功完成可恢复上传，必须发生的一系列交互非常复杂，我怀疑已经有一个包处理这种交换。我用谷歌搜索了一下，找到了 [`google-resumable-media`](https://googlecloudplatform.github.io/google-resumable-media-python/latest/) 包。；-)

`python -m pip install -U google-resumable-media`

我感兴趣的这个包的关键部分是 [`google.resumable_media.requests.ResumableUpload`](https://googlecloudplatform.github.io/google-resumable-media-python/latest/google.resumable_media.requests.upload.html#google.resumable_media.requests.upload.ResumableUpload) 类，它接受一个授权的传输，然后允许你批量上传数据并在检测到错误时恢复。

到目前为止，这是我正在处理的代码:

```
import io
from google.auth.transport.requests import AuthorizedSession
from google.cloud import storage
from google.resumable_media.requests import ResumableUpload

chunk_size = 256 * 1024  # Minimum chunk-size supported by GCS stream = io.BytesIO(b'x' * (1024 * 1024))  # Fake data stream 
client = storage.Client()
bucket = client.bucket('test-bucket')
blob = client.blob('test-blob')

# Create a Resumable Upload url = blob.create_resumable_upload_session(
    content_type='application/octet-stream',
    client=client
)

# Pass the URL off to the ResumableUpload object upload = ResumableUpload(
    upload_url=url,
    chunk_size=chunk_size
)
transport = AuthorizedSession(credentials=client._credentials)

# Start using the Resumable Upload upload.initiate(
    transport=transport,
    content_type='application/octet-stream',
    stream=stream,
    metadata={'name': blob.name}
) 
```

Enter fullscreen mode Exit fullscreen mode

问题是我在`upload.initiate()`得到了一个错误。它抱怨响应中没有`Location`报头。我调查了这个问题，发现`create_resumable_upload_session()`正在做`upload.initiate()`的工作！我删除了这个步骤，而是使用了可恢复上传文档中提供的 API 端点。

```
# Create a Resumable Upload url = (
    f'https://www.googleapis.com/upload/storage/v1/b/'
    f'{bucket.name}/o?uploadType=resumable'
)
upload = ResumableUpload(
    upload_url=url,
    chunk_size=chunk_size
)
transport = AuthorizedSession(credentials=client._credentials)

# Start using the Resumable Upload upload.initiate(
    transport=transport,
    content_type='application/octet-stream',
    stream=stream,
    metadata={'name': blob.name}
) 
```

Enter fullscreen mode Exit fullscreen mode

这个片段开始了一个可恢复的上传！现在开始传输数据。

## 流式数据和`stream_last=False`

`ResumableUpload`对象有一个名为 [`transmit_next_chunk`](https://googlecloudplatform.github.io/google-resumable-media-python/latest/google.resumable_media.requests.upload.html#google.resumable_media.requests.upload.ResumableUpload.transmit_next_chunk) 的方法，它告诉上传者下一个块可能被上传。在阅读关于这个方法的文档时，我发现了`stream_final`，它是 [`ResumableUpload.initiate`](https://googlecloudplatform.github.io/google-resumable-media-python/latest/google.resumable_media.requests.upload.html#google.resumable_media.requests.upload.ResumableUpload.transmit_next_chunk) 方法的一个参数。

我发现如果`stream_final`被设置为`False`，那么当传输的数据块小于其构造函数中设置的`chunk_size`参数时，`ResumableUpload`将检测到流的“结束”。这意味着要传输未知数量的数据，每个数据块必须有> 256KiB，并且必须缓冲输出，直到达到这个大小才能传输。

#### 喜欢这个帖子吗？[查看我的开发博客了解更多。](https://sethmlarson.dev/blog)

## 把所有的东西放在一起

在得到一个简单的工作示例后，我创建了一个类，它处理一个未知长度的数据流，该数据流被逐步上传到一个 blob，如果检测到网络错误，它会从网络错误中恢复。

为了实现这一点，我实现了一个对象，它既缓冲数据，又有一个类似文件的接口，以便被`ResumableUpload`用作`stream`，并被传递给其他需要类似文件的对象来写数据的函数。

下面是我的最终实现:

```
from google.auth.transport.requests import AuthorizedSession
from google.resumable_media import requests, common
from google.cloud import storage

class GCSObjectStreamUpload(object):
    def __init__(
            self, 
            client: storage.Client,
            bucket_name: str,
            blob_name: str,
            chunk_size: int=256 * 1024
        ):
        self._client = client
        self._bucket = self._client.bucket(bucket_name)
        self._blob = self._bucket.blob(blob_name)

        self._buffer = b''
        self._buffer_size = 0
        self._chunk_size = chunk_size
        self._read = 0

        self._transport = AuthorizedSession(
            credentials=self._client._credentials
        )
        self._request = None  # type: requests.ResumableUpload 
    def __enter__(self):
        self.start()
        return self

    def __exit__(self, exc_type, *_):
        if exc_type is None:
            self.stop()

    def start(self):
        url = (
            f'https://www.googleapis.com/upload/storage/v1/b/'
            f'{self._bucket.name}/o?uploadType=resumable'
        )
        self._request = requests.ResumableUpload(
            upload_url=url, chunk_size=self._chunk_size
        )
        self._request.initiate(
            transport=self._transport,
            content_type='application/octet-stream',
            stream=self,
            stream_final=False,
            metadata={'name': self._blob.name},
        )

    def stop(self):
        self._request.transmit_next_chunk(self._transport)

    def write(self, data: bytes) -> int:
        data_len = len(data)
        self._buffer_size += data_len
        self._buffer += data
        del data
        while self._buffer_size >= self._chunk_size:
            try:
                self._request.transmit_next_chunk(self._transport)
            except common.InvalidResponse:
                self._request.recover(self._transport)
        return data_len

    def read(self, chunk_size: int) -> bytes:
        # I'm not good with efficient no-copy buffering so if this is
        # wrong or there's a better way to do this let me know! :-)
        to_read = min(chunk_size, self._buffer_size)
        memview = memoryview(self._buffer)
        self._buffer = memview[to_read:].tobytes()
        self._read += to_read
        self._buffer_size -= to_read
        return memview[:to_read].tobytes()

    def tell(self) -> int:
        return self._read 
```

Enter fullscreen mode Exit fullscreen mode

该类可以这样使用:

```
client = storage.Client()

with GCSObjectStreamUpload(client=client, bucket='test-bucket', blob='test-blob') as s:
    for _ in range(1024):
        s.write(b'x' * 1024) 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！