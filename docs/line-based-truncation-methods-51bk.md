# 基于行的截断方法

> 原文：<https://dev.to/carlymho/line-based-truncation-methods-51bk>

我经常被要求将我建立的网站上的内容摘录删减到一定的行数。虽然这听起来像是一个简单的任务，但由于两个主要原因，它很难实现:首先，因为我们建立的网站可以根据各种设备大小进行缩放，我们不能假设内容框在任何给定的屏幕大小下都是相同的宽度；其次，除非我们使用等宽字体，否则任何文本占用的行数都取决于内容，因为大多数字体中的字符都有不同的宽度。

让我们以这段代码为例:

```
<article>
    <h3>This is a really long heading that we’re going to need to truncate for the sake of the layout since it goes on for several lines.</h3>
    <div class=“excerpt”>
        <p>Cras dolor orci, mollis eget mi vel, faucibus malesuada velit. Phasellus egestas nec nisi non bibendum. Vestibulum faucibus mauris ac ipsum viverra volutpat. Sed lobortis justo sapien, eget ornare orci convallis ut. Nullam pulvinar, urna at faucibus dignissim, orci elit varius quam, non sodales lacus elit a nunc. Morbi eleifend velit quis tellus tempus, sed vehicula neque vulputate. Vestibulum sit amet tempus nibh, sit amet semper mi. Integer sit amet quam eget ligula luctus pulvinar at non ligula. Suspendisse a fringilla lorem, cursus sodales odio. Aliquam ac odio eget nulla consectetur dictum eu quis massa. Sed volutpat ante non felis condimentum vestibulum. In tempor tristique varius. Nunc est nunc, tincidunt quis metus et, semper molestie eros. <a href=“#” class=“readmore”>Read More</a>
    </div>
</article> 
```

Enter fullscreen mode Exit fullscreen mode

## 我们可以采取哪些方法？

### 基于 CSS 的裁剪

一个非常简单的解决方案是使用 CSS 来设置文本所在容器的最大高度。如果我们知道行高，我们可以用它乘以我们想要显示的行数，得到盒子正确剪裁的高度。

```
h3 {
    line-height: 26px;
    height: 52px;
    overflow: hidden;
}

.content {
    line-height: 24px;
    height: 100%;
    overflow: hidden;
    max-height: 72px;
}

.content p {
    margin: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案不需要 javascript，因此性能非常好。您还可以添加少量的 javascript，通过设置。内容容器的高度可能比其中的任何内容都要长，例如 9999999px，这对于过渡动画也很友好。

但是，如果您需要在末尾包含一个“More”或“Continue”链接，或者想要添加一个省略号来表示文本已被截断，那么您将需要一些更健壮的东西，因为这个解决方案将隐藏恰好超过指定行数的段的末尾。

**优点:**性能变化最小，不需要修改标记
**缺点:**不能在文本末尾使用 readmore 链接或省略号，具体到某些 CSS 选择器

### 基于 Javascript 的裁剪

通过使用 Javascript(在本例中是 jQuery，尽管我确信您可以不用它来编写)来操作 HTML 文档，我们可以获得更灵活的结果。

在这种情况下，如果我们知道元素的行高，并且它保持不变，我们可以分离出任何 readmore 链接，按空格分割文本，然后迭代每个单词，直到我们发现内容现在比我们希望它适合的框高。我们还应该将原始文本存储在一个属性中，以便在容器大小改变时更新摘录。

```
$(window).on(‘resize load’, function() {
    $(‘.content p’).each(function() {
        var lineHeight = 16; // content's line-height
        var lines = 3; // number of lines to show
        // only truncate if the text is longer than the desired size; if not, skip it
        if ($(this).height() > lineHeight * lines) {
            if ($(this).find('a.readmore').length > 0) {
                    $(this).attr('data-link', $(this).find('a.readmore')); // if there's a readmore link, separate it out and put it on a data attribute
                    $(this).find('a.readmore').detach(); // remove the link from the HTML
            }
            if ($(this).attr('title')) {
                    $(this).text($(this).attr('title'));
            }
            $(this).attr('title', $(this).text().trim()); // set the text as the title attribute to preserve it
            $(this).text(""); // empty the content
            var str = "";
            var prevstr = "";
            var words = text.split("  "); // split text into words
            var link = this.attr('data-link');
            for (var i = 0; i < words.length; i++) {
                if (this.height() > lines * lineHeight) {
                    // if we've spilled over onto the next line, roll it back a word and break the loop
                    this.html(prevstr.trim() + "&hellip; " + (typeof link !== 'undefined' ? '  ' + link : ''));
                    break;
                }
                prevstr = str;
                // update the working string with the next word
                str += words[i] + "  ";
                // update the content in the document
                this.html(str.trim() + "&hellip;" + (typeof link !== 'undefined' ? '  ' + link : ''));
            }
            // if the last iteration caused us to spill over a line, roll back one word
            if (this.height() > lines * lineHeight) {
                this.html(prevstr.trim() + "&hellip; " + (typeof link !== 'undefined' ? '  ' + link : ''));
            }
        }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

**优点:**可以维护 readmore 链接和省略号
T3】缺点:特定于某些 CSS 选择器

### 任意项上的 Javascript 截断

上面的解决方案非常灵活，但是需要指定行高。如果我们想将解决方案应用于任意元素，或者如果这个特定元素在一些 CSS 断点处指定了不同的行高，该怎么办？

有了许多属性，人们就可以从普通的 Javascript 或通过使用$(elt)获得属性。CSS(“line-height”)方法，但是许多浏览器返回的行高值略有不同，而且，我们不能保证行高将采用哪种单位。

我希望我有一个任何人都可以 DIY 的非常简单的出色的解决方案，但是我非常累，只是下载了[行高](https://www.npmjs.com/package/line-height)模块，并在我自己的脚本之前包含了它。(不出所料，*编写行高模块也是为了补充基于行的截断解决方案。)*

使用它，我们可以用`window.lineHeight(this[0]);`替换分配给 lineHeight 变量的静态数字，它应该以一种易于使用的方式返回行高值。在这一点上，这很容易变成一个自定义函数，我们可以用一个元素和给定的行数来调用它，或者甚至作为一个 jQuery 插件，我们可以在任何元素上使用它作为一个方法。

**优点:**维护 readmore 链接和省略号，可以轻松重用
**缺点:**使用一个外部的行高库

## 试玩！

下面是放在 Codepen 演示中的全部内容，所有内容都打包到一个 jQuery 插件中:

[https://codepen.io/cmho/embed/jZzQRg?height=600&default-tab=result&embed-version=2](https://codepen.io/cmho/embed/jZzQRg?height=600&default-tab=result&embed-version=2)