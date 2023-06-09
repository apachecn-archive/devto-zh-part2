# 凤凰城的外部服务测试

> 原文：<https://dev.to/vinhnglx/external-service-testing-in-phoenix-3ehg>

## 入门

在进入细节之前，让我分享一下我们的系统——我们正在使用 Elixir-Phoenix 框架来构建一个后端系统，根据需求，我们需要构建一个 API 来支持我们的前端客户端(ReactJS/React-Native)将文件上传到 AWS_S3。

在 Phoenix framework 中，我们使用 AWS 客户端的十六进制包 [ex_aws](https://github.com/ex-aws/ex_aws) 将文件上传到 S3。基本上，控制器代码将:

```
unique_filename = "#{UUID.uuid4(:hex)}_#{filename}"

{:ok, image_binary} = filepath |> File.read()

Application.get_env(:my_app, :image_bucket_name) |> ExAws.S3.put_object(unique_filename, image_binary) |> ExAws.request!() 
```

Enter fullscreen mode Exit fullscreen mode

如果上传成功，`ExAWS.request!()`将返回`status_code`为`200`，否则将返回另一个`status_code`。

## 上传模块

像往常一样，我们将上传代码从 UploadController 移动到 UploadService 模块——这将使控制器看起来更易读，更容易编写测试。

```
defmodule MyApp.UploadController do
  use MyApp, :controller

  @upload_service Application.get_env(:my_app, :upload_service)
  import UploadService

  def upload_image(conn, params) don
    case upload(params) do
      {:ok, filename} ->
        json(conn, %{url: resolve_url(filename), error: nil})

      {:error, reason} ->
        json(conn, %{url: nil, error: reason})
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
defmodule UploadService do
  def upload(params) do
    %{"file" => %{filename: filename, path: filepath}} = params
    unique_filename = "#{UUID.uuid4(:hex)}_#{filename}"
    {:ok, image_binary} = filepath |> File.read()

    case Application.get_env(:my_app, :image_bucket_name)
         |> ExAws.S3.put_object(unique_filename, image_binary)
         |> ExAws.request!() do
      %{status_code: 200} ->
        {:ok, unique_filename}

      _ ->
        {:error, "can't upload"}
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 为上传 API 编写测试

> 当与外部服务集成时，我们希望确保我们的测试套件不会影响任何第三方服务。我们的测试应该独立运行。[思维机器人](https://robots.thoughtbot.com/how-to-stub-external-services-in-tests)

使用我们的 UploadService 模块，我们不需要测试对 S3 的请求，因为包本身已经测试过了。所以，我们只需要模仿模块返回`ok`或`error`响应。

### 为不同的环境设置相应的模块

开发和生产环境将使用真实的`UploadService`，测试环境将使用`UploadService.Mock`。

```
# dev.exs and prod.exs
config :my_app, :upload_service, UploadService

# test.exs
config :my_app, :upload_service, UploadService.Mock 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们在控制器中做了一点改变来动态加载相应的模块。

```
defmodule MyApp.UploadController do
  use MyApp, :controller

  @upload_service Application.get_env(:my_app, :upload_service)
  @upload_image_token Application.get_env(:my_app, :upload_image_token)

  def @upload_image_token.upload_image(conn, params) don
    case upload(params) do
      {:ok, filename} ->
        json(conn, %{url: resolve_url(filename), error: nil})

      {:error, reason} ->
        json(conn, %{url: nil, error: reason})
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将创建`UploadService.Mock`模块。

### 创建嘲讽模块

```
defmodule UploadService.Mock do
  def upload(%{"file" => %{
    "filename" => "success", "path" => _path
  }}) do
    {:ok, "your-file.png"}
  end

  def upload(%{"file" => %{
    "filename" => "fail", "path" => _path
  }}) do
    {:error, "can't upload"}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们使用不同的`filename`模式匹配来返回`ok`或`error`。

### 写测试

而且现在考试也不难写了。

```
test "uploads success", %{image_token: image_token, conn: conn} do
  conn = put_req_header(conn, "authorization", image_token)
  file = %{
    "filename" => "success",
    "path" => "/your/image/path"
  }
  response =
    post(
      conn,
      "/upload",
      %{
        "file" => file
      }
    )

  assert %{"error" => nil, "url" => _url} = json_response(response, 200)
end

test "uploads fail", %{image_token: image_token, conn: conn} do
  conn = put_req_header(conn, "authorization", image_token)
  file = %{
    "filename" => "fail",
    "path" => "/your/image/path"
  }
  response =
    post(
      conn,
      "/upload",
      %{
        "file" => file
      }
    )

  assert %{"error" => "can't upload", "url" => nil} = json_response(response, 200)
end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这就是我们在不触及外部服务的情况下编写 API 测试的方式。这可能不是一个好办法，所以如果你们有任何想法，不吝赐教。

谢谢大家！