# 客户端 HTML/CSS 预处理

> 原文：<https://dev.to/genejams/client-side-htmlcss-pre-processing-12h>

 <small>虽然这篇文章中的主题可能仍然是真实的，但是这些内容引用了一个旧版本的 *zuix.js* 库。最新文档见 [zuix.js](https://zuixjs.org) 网站。</small> 

* * *

客户端*预处理*的任务是*在将某些内容或样式文件实际添加到 [DOM](https://www.w3schools.com/js/js_htmldom.asp) 之前对其进行转换*。

这种“转换”实践在很多情况下都是有用的，这里只提到一些常见的用途:

*   将[降价](https://www.markdownguide.org/basic-syntax)文本转换为 HTML
*   用匹配的变量值替换*花括号*(模板变量)
*   编译 [SCSS，少或手写笔](https://htmlmag.com/article/an-introduction-to-css-preprocessors-sass-less-stylus)增强样式到标准 CSS

在这篇文章中，我将描述如何在*客户端*上实现这种处理，尽管这也可以通过服务器端脚本或构建工具来完成。

我将使用`zuix.js`库作为这个例子的支持工具，但是这里描述的概念和方法应该与使用任何其他实用程序库、框架或 VanillaJS 几乎相同。

## 实现步骤

1.  第一步是下载原始(未处理的)资源。
2.  然后我们可以处理和转换原始数据。
3.  最后，可以将结果添加到 DOM 中。

这些步骤可以用 VanillaJS 实现，方法是发出 AJAX 请求，用老方法，或者用现代的`fetch`方法。

```
// 1) Download
fetch('//some.host/some/url', {
  method: 'get'
}).then(function(rawData) {
  // 2) pre-process
  let content = preProcess(rawData);
  // 3) attach to DOM 
  const el = document.createElement('div');
  el.innerHTML = processedContent;
  container.appendChild(el);
}); 
```

Enter fullscreen mode Exit fullscreen mode

参见 [David Walsh](https://davidwalsh.name/fetch) 博客，了解关于 *AJAX/fetch* 主题的更多信息。

但是在基于组件的开发中，我们将利用*生命周期*事件处理器，特别是*全局钩子*。

## 全局挂钩

在下面的例子中，主`index.html`文件包含一些使用`data-ui-include`属性的内容。

**index.html(片段)**

```
<!-- Content View 1 -->
<div data-ui-include="content/lorem_ipsum_1"></div>
<!-- Content View 2 -->
<div data-ui-include="content/lorem_ipsum_2"></div>
<!-- Content View 3 -->
<div data-ui-include="content/lorem_ipsum_3"></div> 
```

Enter fullscreen mode Exit fullscreen mode

`data-ui-include`属性指示 *zuix.js* 加载以下文件:

```
/* View 1 */
./content/lorem_ipsum_1.html
./content/lorem_ipsum_1.css
/* View 2 */
./content/lorem_ipsum_2.html
./content/lorem_ipsum_2.css
/* View 3 */
./content/lorem_ipsum_3.html
./content/lorem_ipsum_3.css 
```

Enter fullscreen mode Exit fullscreen mode

这些*。html* 文件包含 *MarkDown* 文本和一些模板变量，而*。css* 文件正在使用少*多*的语法。

装载好每一辆*后。html* 文件， *zuix.js* 会触发`html:parse`全局钩子处理程序，而对于每个*。css* 文件它将触发`css:parse`处理程序。

仅供参考，这是加载内容(`data-ui-include`)或组件(`data-ui-load`)时发生的生命周期步骤列表:

```
GLOBAL HOOKS
Content/Component loading life-cycle
  // detached state
  ↓ HTML file loaded
    ⚡ 'html:parse'
  ↓ CSS file loaded
    ⚡ 'css:parse'
  // attached state
  ↓ Model to View
    ⚡ 'view:process'
  ↓ Controller setup
    ⚡ 'component:ready' 
```

Enter fullscreen mode Exit fullscreen mode

因此，简单地说，用 *zuix.js* 进行预处理就是注册两个钩子处理程序:

```
zuix.hook('html:parse', function(data) {

  // TODO: process and replace 'data.content'

}).hook('css:parse', function(data) {

  // TODO: process and replace 'data.content'

}); 
```

Enter fullscreen mode Exit fullscreen mode

出于实际代码使用的目的

*   [摊牌](http://showdownjs.com/) -降价到 HTML 转换器
*   `zuix.$.replaceBraces`基本模板变量的方法
*   [LESS](http://lesscss.org/) - CSS，只多一点点

如下面的`index.js`文件所示:

```
const fields = {
  'title': 'Quam magna gratus',
  'subtitle': 'Haberent obstat animi non sine vestigia tristis',
  'disclaimer': 'Random text generated with Lorem Markdownum.',
  'copyright': '&copy; Mickey Mouse and Associates'
};
zuix.hook('html:parse', function(data) {

  // Replace {{braces}} fields
  const parsed = zuix.$.replaceBraces(data.content, function(name) {
    // remove braces from '{{name}}'
    name = name.replace(/([{}])/g, '');
    // lookup value in `strings` object
    if (fields[name] != null) {
      return fields[name];
    }
  });
  if (parsed != null) data.content = parsed;

  // ShowDown - Markdown compiler
  data.content = new showdown.Converter().makeHtml(data.content);

}).hook('css:parse', function(data) {

  less.render(data.content, function(err, out) {
    data.content = out.css;
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在下面看到工作示例并浏览其源代码:

[https://glitch.com/embed/#!/embed/zuix-hooks-1?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/zuix-hooks-1?previewSize=100&path=index.html)

在这个例子中，任何包含的内容总是要经过预处理，但是大多数时候最好是显式地设置一个选项来触发预处理。
在这种情况下，我们可以使用`data-ui-option`属性，并向其传递一个包含所有所需标志的对象。

**index.html(片段)**

```
<!-- Only the first include will be processed -->
<div data-ui-include="content/lorem_ipsum_1"
     data-ui-options="options.process"></div>
<div data-ui-include="content/lorem_ipsum_2"></div>
<div data-ui-include="content/lorem_ipsum_3"></div> 
```

Enter fullscreen mode Exit fullscreen mode

这是 **index.js** 文件
的修改版

```
window.options = {
  process: {
    markdown: true,
    fields: {
      'title': 'Quam magna gratus',
      'subtitle': 'Haberent obstat animi non sine vestigia tristis',
      'disclaimer': 'Random text generated with Lorem Markdownum.',
      'copyright': '&copy; Mickey Mouse and Associates'
    },
    less: true
  }
};
zuix.hook('html:parse', function(data) {

  const fields = this.options().fields;
  if (fields != null) {
    // Replace {{braces}} fields
    const parsed = zuix.$.replaceBraces(data.content, function(name) {
      // remove braces from '{{name}}'
      name = name.replace(/([{}])/g, '');
      // lookup value in `fields` object
      if (fields[name] != null) {
        return fields[name];
      }
    });
    if (parsed != null) data.content = parsed;
  }

  if (this.options().markdown) {
    // ShowDown - Markdown compiler
    data.content = new showdown.Converter().makeHtml(data.content);
  }

}).hook('css:parse', function(data) {

  if (this.options().less) {
    less.render(data.content, function(err, out) {
      data.content = out.css;
    });
  }

}); 
```

Enter fullscreen mode Exit fullscreen mode

所以，暂时就这些了。是时候出去呼吸点新鲜空气了=)

阅读下一篇:

[![genejams](img/ab410dc05437d55e52306d6bb995095d.png)](/genejams) [## 准备使用渐进式 Web 应用程序模板

### { Gene } Aug 13 ' 183 分钟读取

#showdev #webdev #javascript #zuixjs](/genejams/ready-to-use-progressive-web-app-template--18o5)