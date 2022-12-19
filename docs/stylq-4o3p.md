# 风格

> 原文：<https://dev.to/aj1thkr1sh/stylq-4o3p>

标题:Stylq
发布:true
描述:Stylq 是很酷的语法 HTML 预处理器。Stylq 将允许用户更容易地制作 HTML 文件。Stlyq 是以简单的方式生成 HTML 文件的丰富语法文件。Stylq 易于阅读和书写。

用法和示例

```
 var stylq = require('stylq');

  stylq.process('sample.stylq'); 
```

Enter fullscreen mode Exit fullscreen mode

发送到另一个位置

```
 stylq.processAndSend('sample.stylq','targetFileName.html'); 
```

Enter fullscreen mode Exit fullscreen mode

下面是作为输入给出的示例文件。

```
 //sample.stylq

  html{
    head{
      title{
        The Title of the Page
      }
    }
    body{
      h1{
        This is header
      }
      p id="paragraph-id"{
        This is paragraph
      }
      h2 style="color: blue;" id="heading-line"{
        The Multi attributes
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

[【NPM】](https://www.npmjs.com/package/stylq)
[【yar】](https://yarnpkg.com/en/package/stylq)
标记:js，stylq，webdev，html