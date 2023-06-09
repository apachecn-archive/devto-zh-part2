# 读取本地和网络文件的统一代码

> 原文：<https://dev.to/notwoods/unifying-code-to-read-local-and-network-files-31m2>

[![Imgur upload page with Browse button and input to paste URL](img/a0cd25f8290da82fa438aedccec5fffa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wVmfKscH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tigeroakes.com/posts/unify-local-network-file-reading/imgur-upload.png)

许多 web 应用程序允许用户上传文件或输入 URL 来下载文件。
上传文件时，HTML 文件输入让你使用 JavaScript 中的`FileReader` API 读取文件。
从网络下载时，使用`fetch`功能。
根据我在谷歌的经理给我的提示，我知道了一个简单的技巧，可以对本地和网络文件使用相同的代码。

## 如何读取本地文件

JavaScript 将本地文件表示为[`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
blob 表示原始二进制数据，在其他一些地方也可以看到，比如 [SQLite](https://www.sqlite.org/datatype3.html#storage_classes_and_datatypes) 。对于一个文件，它们将其内容表示为需要转换为文本的二进制数据。

JavaScript 还提供了 [`FileReader`](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) 对象，一个读取文件的 API(就像它在 tin 上说的那样)。
它可以与文件选择器输入或拖放目标配对。
我现在只关注文件选择器。
可以这样用:

```
async function loadLocalFile(file) {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = event => {
            resolve(event.target.result);
        };
        reader.onerror = event => {
            reject(event.target.error);
        };
        reader.readAsText(file);
    });
}

form.onsubmit = async () => {
    const data = await loadLocalFile(fileInput.files[0]);
    console.log(data);
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 网络文件是如何读取的

互联网上的每一页都由一个 URL(统一资源定位符)标识，看起来有点像`https://example.com/page.html`。
URL 指向互联网上的一个位置，网络浏览器用它来查找一个网站的位置，然后把它的内容显示给你。

在现代 JavaScript 中，使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 从另一个网站页面获取数据非常容易。
你只需向`fetch`函数传递一个 URL，它就能让你等待网页上的文本被下载。

```
async function loadNetworkFile(url) {
    const response = await fetch(url);
    return fetch.text();
}

form.onsubmit = async () => {
    const data = await loadNetworkFile(urlInput.value);
    console.log(data);
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 统一代码

许多 web 应用程序允许您从电脑中选择一个文件，或者指定一个 URL 来下载它。
这在图片上传中很常见，但我在许多不同的文件类型中都见过。
在我的项目 Super Size 中，我想让用户通过 URL 或本地文件指定要加载的 JSON 数据，而不需要复制大量代码。
如果可以用 fetch API 读取`File`对象，提供一种简单统一的方法来加载带有承诺的数据，那就太理想了。
事实证明你可以做到！

您可以使用 [`URL.createObjectURL()`](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL) 功能创建一个指向 Blob 的 URL。
该功能通常用于显示上传的图像，但适用于任何文件类型。
产生的 URL 可以被 Fetch API 使用，因此您可以在本地文件中重用网络代码。

有一个问题是，URL 需要在以后被释放，否则，通过保持一个指向 Blob 对象的指针，就会出现内存泄漏。
你可以用 [`URL.revokeObjectURL()`](https://developer.mozilla.org/en-US/docs/Web/API/URL/revokeObjectURL) 功能来完成。

```
async function loadNetworkFile(url) {
    const response = await fetch(url);
    return fetch.text();
}

async function loadLocalFile(file) {
    const url = URL.createObjectURL(file);
    const data = await loadNetworkFIle(url);
    URL.revokeObjectURL(url);
    return data;
}

form.onsubmit = async () => {
    let data;
    if (urlInput.value) {
        data = await loadNetworkFile(urlInput.value);
    } else {
        data = await loadLocalFile(fileInput.files[0]);
    }
  console.log(data);
}; 
```

Enter fullscreen mode Exit fullscreen mode

你可以在超级尺寸的源代码中看到一个更复杂的例子。
你也可以在[直播应用](https://storage.googleapis.com/chrome-supersize/index.html)上看到它的运行。