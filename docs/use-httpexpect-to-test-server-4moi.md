# 使用 httpexpect 测试服务器

> 原文：<https://dev.to/dannypsnl/use-httpexpect-to-test-server-4moi>

使用内置功能来测试 Go 服务器是一种痛苦经历。问题是因为我们必须处理太多的错误，得到太多我们并不总是需要的东西。

当然，我们会创建一个抽象来减少这种恐慌。但是如果我们已经有了成熟的解决方案呢？那就是 [httpexpect](https://github.com/gavv/httpexpect)

我们有两个导入选项:

*   `import "github.com/gavv/httpexpect.v1"`
*   `import "github.com/gavv/httpexpect"`

不同的是`v1`是稳定分支，另一个是 GitHub 上的`master`分支。

我建议为公司项目选择一个稳定的，但在副项目使用`master`分支也可以。

`httpexpect`和`httptest`配合的很好。一个简单的例子:

```
type fakeHandler struct {
}

func (h *fakeHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    fmt.Fprint(w, "Hello")
}

func TestIt(t *testing.T) {
    handler := &fakeHandler{}
    server := httptest.NewServer(handler)
    defer server.Close()

    e := httpexpect.New(t, server.URL)

    e.GET("/").
        Expect().
        Status(http.StatusOK).
        Body().Equal("Hello")
} 
```

Enter fullscreen mode Exit fullscreen mode

添加表单值怎么样？

```
e.PATCH("/test/patch").WithFormField("value", "patch").
    Expect().Status(http.StatusOK).
    Body().Equal("patch") 
```

Enter fullscreen mode Exit fullscreen mode

JSON 回应？

```
e.POST("/post").
    Expect().Status(http.StatusOK).
    ContentType("application/json", "").
    JSON().Equal(expected) 
```

Enter fullscreen mode Exit fullscreen mode

查询？

```
e.GET("/user").WithQuery("name", "Danny").
    Expect().Status(http.StatusOK). 
```

Enter fullscreen mode Exit fullscreen mode

希望你会对这个小小的介绍感到高兴。