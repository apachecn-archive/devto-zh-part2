# 编写我自己的 terraform 模板(数据)提供程序

> 原文：<https://dev.to/ineedale/writing-my-own-terraform-template-data-provider-loa>

作为我之前关于如何[使用 terrafrom 内置循环机制](https://dev.to/ineedale/terraform-templating-and-loops-479p)的帖子的后续，我开始编写自己的自定义“数据”提供者，在这种情况下，一个新的模板渲染器将允许我使用 Golang 的文本/模板，而不是 terraform 自己的。

TL；博士——代码在这里..

Alex-leonhardt/terra form-provider-gotemplate

我开始尝试按照 Hashicorp 的 [*编写定制提供者*](https://www.terraform.io/docs/extend/writing-custom-providers.html) 文档，然而这似乎并没有解释如何编写一个 ***数据*** ***提供者*** ，尽管我有一个基本的 Hello World 工作，但我并没有太接近我实际想要做的。

所以，既然已经有了一个模板提供者，我选择以此为起点，把我不想要/不需要的部分全部去掉。这可能意味着我没有遵循*【最佳实践】*。我想要的很简单，我将一个 json 编码的变量传递给 go-template 提供者，它将读取一个模板文件，呈现它(或者失败)，我应该能够使用。呈现的方法，就像原始模板提供程序一样。

要使用它，它大概看起来像这样…

```
variable "data" {
 default = {
 "msg" = "Hello World"
 "msg2" = [1, 2, 3, 4]
 }
}

data "gotemplate" "gotmpl" {
template = "${path.module}/file.tmpl"
data = "${jsonencode(var.data)}"
}

output "tmpl" {
value = "${data.gotemplate.gotmpl.rendered}"
} 
```

正如你所看到的，我们有一个混合了字符串类型键值的映射变量“数据”,其他类型似乎是不允许的——参见[https://stackoverflow.com/a/8758771](https://stackoverflow.com/a/8758771)

我决定，2 个变量应该足够了，模板文件(template)的路径和 json 编码的数据(data)本身。我可能不会深入讨论提供者应该如何构造的细节，因为 TF 的模板提供者似乎与*“编写定制提供者”*指令有点不同——可能是因为它有点老，可能是最早的一个。因此，自从我(或多或少)遵循了这个例子，我的新供应商也没有遵循它，至少没有密切遵循。

要使用我的新提供者，所需要的就是将其编译成一个名为 terraform-provider-gotemplate 的文件，我可以使用 gotemplate 提供者，这在 main.go 文件中指定

```
// Provider is a TF provider
func Provider() *schema.Provider {
 return &schema.Provider{
 DataSourcesMap: map[string]*schema.Resource{
 "gotemplate": dataSourceFile(),
 },
 }
} 
```

真正重要的部分在 dataSourceFile()中，它指定了可以使用提供者访问哪些变量，在我的例子中就是这样

*   模板(go 模板文件的路径)
*   数据(json 编码的数据)
*   渲染(完成的渲染)

除了设置模式，我还必须让它实际做一些事情，这是用
完成的

```
func dataSourceFile() *schema.Resource {
 return &schema.Resource{
 Read: dataSourceFileRead, 
```

它将 dataSourceFileRead 函数传递给模式。资源的读取字段(或属性？我喜欢把它想成一个属性)它必须匹配这个函数签名

```
type ReadFunc func(*ResourceData, interface{}) error 
```

这是用
完成的

```
func dataSourceFileRead(d *schema.ResourceData, meta interface{}) error {
 rendered, err := renderFile(d)
 if err != nil {
 return err
 }
 d.Set("rendered", rendered)
 d.SetId(hash(rendered))
 return nil
} 
```

d.Set 对于使用完成的呈现模板设置呈现变量很重要，因此它可以作为输入与输出或其他提供程序一起使用。

在设置 rendered 之前，我们通过调用 renderFile(d)函数来检索渲染输出，如下所示