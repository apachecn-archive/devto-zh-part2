# 用 Blazor 上传文件

> 原文：<https://dev.to/rembou1/upload-file-with-blazor-6o9>

# 在 Blazor 应用中上传文件

Blazor 是一个 SPA 框架。在 web 应用程序的某些时候，您需要将文件上传到服务器。以下是你如何用 Blazor 做到这一点。

现在，Blazor 不支持开箱即用，我们必须用 js 加载文件内容，用 C#获取这些内容，并将其发送到服务器。

## 布拉索身边

你需要一个文件输入，并用 Blazor/C#
处理 onchange 事件

```
<input type="file" onchange="@UploadFile" id="fileUpload" />
 async Task UploadFile()
    {
        var data = await JsInterop.GetFileData("fileUpload");
        var response = await Http.PostAsync("/api/upload",new ByteArrayContent(Convert.FromBase64String(data)));
        var fileTempName = await response.Content.ReadAsStringAsync();
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   我们将从 javascript 函数接收 base64 格式的二进制文件，因为字节数组在 json 中是不可序列化的，这是 C#和 js 之间的交换格式。
*   api 将只发送回保存文件的文件名，但是你可以做你想做的。

## JS 端

下面是获取文件内容
的方法

```
const readUploadedFileAsText = (inputFile) => {
    const temporaryFileReader = new FileReader();
    return new Promise((resolve, reject) => {
        temporaryFileReader.onerror = () => {
            temporaryFileReader.abort();
            reject(new DOMException("Problem parsing input file."));
        };
        temporaryFileReader.addEventListener("load", function () {
            var data = {
                content: temporaryFileReader.result.split(',')[1]
            };
            resolve(data);
        }, false);
        temporaryFileReader.readAsDataURL(inputFile.files[0]);
    });
};
Blazor.registerFunction("getFileData", function (inputFile) {
    var expr = "#" + inputFile.replace(/"/g, '');
    return readUploadedFileAsText($(expr)[0]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   我不得不做这个奇怪的替换，因为 Blazor 中有一个关于异步 js 互操作调用的错误(它不是传递“输入文件”，而是传递“输入文件”)
*   我使用了一个承诺，因为我们不能在 js 中同步读取文件，Blazor 需要一个承诺来调用异步 js 方法
*   这段代码很大程度上受到了[https://blog . shovon Hasan . com/using-promises-with-file reader/](https://blog.shovonhasan.com/using-promises-with-filereader/)的启发，它很有帮助，因为我发现很难理解 promise。
*   我们可以很容易地移除 jquery 依赖性
*   readAsDataURL 将信息附加到 base64 文件中，所以我们必须将它拆分，得到第二部分

### Blazor JS 互操作

现在 C#和 JS 之间的桥梁

```
public static async Task<string> GetFileData(string fileInputRef)
        {
            return (await RegisteredFunction.InvokeAsync<StringHolder>("getFileData", fileInputRef)).Content;
        } 
```

Enter fullscreen mode Exit fullscreen mode

*   我必须创建一个简单的 StringHolder，因为 js interop 还不支持字符串交换

### 文件保存在服务器上

这里是用来在服务器上保存文件内容的代码，但是一旦你有了二进制文件，你就可以用它做你想做的事情

```
[Authorize, Route("api/upload")]
    public class UploadController : ControllerBase
    {
        [HttpPost]
        public async Task<IActionResult> Save()
        {
            var tempFileName = Path.GetTempFileName();
            using (var writer = System.IO.File.OpenWrite(tempFileName))
            {
                await Request.Body.CopyToAsync(writer);
            }
            return Ok(Path.GetFileNameWithoutExtension(tempFileName));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*   我喜欢使用路径 API 来管理这类文件
*   遗憾的是，我们不能将二进制文件作为方法参数(我讨厌那些请求/响应大型类属性)

你可以在我的 Toss 项目这里[https://github.com/RemiBou/Toss.Blazor](https://github.com/RemiBou/Toss.Blazor)找到并执行这段代码。

### 引用

*   [https://blog . shovonhasan . com/using-promises-with-filereader/](https://blog.shovonhasan.com/using-promises-with-filereader/)
*   [https://blazor.net/docs/javascript-interop.html](https://blazor.net/docs/javascript-interop.html)
*   [https://github.com/aspnet/Blazor/issues/527](https://github.com/aspnet/Blazor/issues/527)
*   [https://github.com/aspnet/Blazor/issues/479](https://github.com/aspnet/Blazor/issues/479)
*   [https://stack overflow . com/questions/32556664/getting-byte-array-through-input-type-file/32556944 # 32556944](https://stackoverflow.com/questions/32556664/getting-byte-array-through-input-type-file/32556944#32556944)