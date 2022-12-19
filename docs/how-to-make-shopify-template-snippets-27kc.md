# 如何制作 Shopify 模板片段

> 原文：<https://dev.to/whoisryosuke/how-to-make-shopify-template-snippets-27kc>

可定制的组件是任何现代平台的关键。用户有一个期望，能够适应和改变他们的网站使用可回收设计组件的一部分，他们的主题系统。用户应该能够动态编辑他们的滑块或页面布局，根据需要自由移动和编辑内容，无需开发经验。

它是像 ***Squarespace*** 和 ***Wix*** 这样的服务所包含的东西。甚至 ***Wordpress*** 也试图用他们的 *Gutenberg* 编辑器实现它的一个版本，我们已经用 *WPBakery Page Builder* (以前的 *Visual Composer* )插件实现它很多年了。

> 网站是一个组件的集合，没有理由我们不能从代码中抽象出控制，并且只需点击一下。

Shopify 用他们的“**片段**提供了一个版本。代码片段是可重用的组件，可以包含在任何模板文件中。在网站的首页，您可以使用可定制的代码片段来构建主登录页面的布局。

## 它是如何工作的？

在首页上，这与类似于 *Squarespace* 或 *Wix* 的事情非常相似。你有 ***【内容块】*** ，每个块是一个**片段**，你可以按照你喜欢的任何顺序排列它们。如果你点击一个区块，它会显示更多的选项，比如插入文本或图像。

[![The widget](../Images/617288c3d5fa1ca738e1e4755cc89094.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_tQtIo76--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://stayregular.net/content/2-blog/20180409-how-to-make-shopify-template-snippets/shopify-admin1.png)

[![A block inside the widget](../Images/60da4b014d95926a9e9b9e7ab518b9bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sGuJ5p1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://stayregular.net/content/2-blog/20180409-how-to-make-shopify-template-snippets/shopify-admin2.png)

然而，在任何其他页面上，它仍然需要您手动将 include 片段插入到您的 Liquid page 模板(`{% section 'your-snippet-here' %}`)中，并且它不提供相同级别的定制。您可以插入变量，但它是一个不同于块设置的 API，所以您也必须考虑这一点。

[![](../Images/5b0924ef1c7a5f589571bfdedc6e00da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ytjpKcC7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://stayregular.net/content/2-blog/20180409-how-to-make-shopify-template-snippets/shopify-snippet.png)

## 制作片段

创建代码片段很容易。打开您的 Shopify 主题文件夹，导航到 snippets 文件夹，并使用您喜欢的任何 HTML 创建一个新的 Liquid 文件。这对于在整个网站上重复的组件来说是非常好的，比如时事通讯块或者侧边栏小部件。

下面是一个包含文本标题和以图像为背景的框的柔性网格的块的例子:

```
<div class="page-width">
  <div class="section-block">
      <div class="section-block __header section-block__ header--padded text-center">
        <h4 class="h1--mini section-block__title">Text columns with images</h4>
      </div>

    <div class="flex column-flex">
        <div class="flex__item column-overlay text-center">
            <noscript>
              <div class="column-flex__image" style="background-image: url(http://images.google.com/imageurl.jpg); background-position: center center;"></div>
            </noscript>
            <div class="column-flex__image lazyload"
              style="background-position: center center; background-image: url('http://images.google.com/imageurl.jpg);">
            </div>
          <div class="column-flex__content">
            <p class="h5">Image Title</p>
          </div>
        </div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 西码海关

定制一个代码片段是事情变得有趣的地方。要使代码片段可定制，您必须在代码片段的底部添加一个**模式**。将以下内容复制并粘贴到当前代码片段的底部:

```
{% schema %}
  {
    "name": "Text columns with images",
    "class": "index-section",
    "max_blocks": 24,
    "settings": [
      {
        "type": "checkbox",
        "id": "title_enable",
        "label": "Show header",
        "default": true
      },
      {
        "type": "text",
        "id": "title",
        "label": "Heading",
        "default": "Text columns with images"
      }
    ],
    "blocks": [
      {
        "type": "text_block",
        "name": "Text",
        "settings": [
          {
            "type": "text",
            "id": "title",
            "label": "Heading",
            "default": "Add a title or tagline"
          },
          {
            "type": "image_picker",
            "id": "image",
            "label": "Image"
          },
          {
            "type": "radio",
            "id": "image_align",
            "label": "Image alignment",
            "options": [
              { "value": "top center", "label": "Top center" },
              { "value": "center center", "label": "Center" },
              { "value": "bottom center", "label": "Bottom center" }
            ],
            "default": "top center"
          }
        ]
      }
    ],
    "presets": [
      {
        "name": "Overlay Columns",
        "category": "Text",
        "blocks": [
          {
            "type": "text_block"
          },
          {
            "type": "text_block"
          },
          {
            "type": "text_block"
          }
        ]
      }
    ]
  }
{% endschema %} 
```

Enter fullscreen mode Exit fullscreen mode

一旦您将它添加到您的代码片段中，如果您转到您的 frontpage 并单击“添加部分”，您应该会在列表中看到您定义的代码片段标题。

## 图式是什么意思？

JSON 的第一部分非常简单明了。**名称**是你将在 Shopify 后端看到的标题。 **max_blocks** 限制了您可以创建的子组件的数量(例如:一篇最近的文章片段限于 9 篇文章)。

**设置数组**是您为代码片段放置顶级配置的地方。在这个例子中，我们在用户自己的头中给用户类型，并启用或禁用它的可见性。

**块数组**定义了额外的*“子块”*，您可以在您的内容块或片段中创建它们。在本例中，我们创建了一个块，用户可以在其中定义标题文本、上传图像和选择图像对齐方式。根据之前定义的`max_blocks`，你可以制作那么多的*【子块】*。

**预设数组**为您的代码片段创建一个“默认”设置。如果小部件为 3 列，并且使用 3 个块效果最佳，则可以创建 3 个样本块。当用户创建一个新的代码片段时，将自动为它们创建 3 个样本块(用您定义的默认值填充)。

## 将变量添加到代码片段中

您可以使用`{{ section.settings.title | escape }}`访问顶级代码片段配置，将标题替换为所需设置的 **ID 名称**。要进入程序块设置，您需要遍历程序块，然后访问 for 循环，就像这样:

```
{% for block in section.blocks %}
      {{ block.settings.title | escape }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

## 如果块包含变量，做点什么

当您将从定制中获得的参数与 Shopify 的 liquid templating 系统结合起来时，只需稍加修改，就可以实现一些相当动态的组件。如果用户没有定义任何块会发生什么？或者忘记上传图片？我们可以用 Liquid 的`{% if %}`来处理所有这些条件案例。

像标题一样，我们如何处理开/关切换？

做一个简单的 if 语句检查变量。如果切换到“on”，变量将为真，因此将执行其中的任何代码:

```
{% if section.settings.title_enable %}
      {{ section.settings.title | escape }}
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

如果图像没有被定义会怎样？

您可以检查变量，看它是否等于(或者在本例中，不等于)`blank`，它是一个 Shopify 值，用于检查该值是否为空。

```
{% if block.settings.image != blank %}
      {{ block.settings.image | img_url: '600x600' }}
{% else %}
      <img src="Placeholder.svg" />
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

**如果用户不创建块怎么办？**

blocks 变量包含一个 size 属性，可以让您看到有多少个块。这种情况下可以检查是否等于 0:

```
{% if section.blocks.size == 0 %}
      {% include 'no-blocks' %}
      {% comment %}
            We include a placeholder snippet that contains something like a statement saying "Sorry, no posts found".
      {% endcomment %}
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要检查网格的一致性，这也很方便(例如:如果 blocks == 3，那么使用 3 列类属性)。

## 最终代码

当我们将几个简单的 if 语句串在一起，并将硬编码的数据与我们的 Shopify 片段配置变量交换时，我们就有了一个完全可定制的块:

```
<div class="page-width">
  <div class="section-block">
    {% if section.settings.title_enable %}
      <div class="section-block __header section-block__ header--padded text-center">
        <h4 class="h1--mini section-block__title">{{ section.settings.title | escape }}</h4>
      </div>
    {% endif %}

    <div class="flex column-flex">
      {% for block in section.blocks %}
        {% if block.settings.image != blank %}
          {% assign img_url = block.settings.image | img_url: '600x600' %}
        {% endif %}
        <div class="flex__item column-overlay text-center" {{ block.shopify_attributes }}>
          {% if block.settings.image != blank %}
            <noscript>
              <div class="column-flex__image" style="background-image: url({{ block.settings.image | img_url: '600x600' }}); background-position: {{ block.settings.image_align }};"></div>
            </noscript>
            <div class="column-flex__image lazyload"
              style="background-position: {{ block.settings.image_align }}; background-image: url('{{ block.settings.image | img_url: '300x300' }});"
              data-bgset="{% include 'bgset', image: block.settings.image %}"
              data-sizes="auto"
              data-parent-fit="cover">
            </div>
          {% else %}
            <div class="column-flex__image">
              <div class="placeholder-background">
                {{ 'placeholder.svg' | asset_url }}
              </div>
            </div>
          {% endif %}
          <div class="column-flex__content">
            <p class="h5">{{ block.settings.title | escape }}</p>
          </div>
        </div>
      {% endfor %}
    </div>

    {% if section.blocks.size == 0 %}
      {% include 'no-blocks' %}
    {% endif %}
  </div>
</div>

{% schema %}
  {
    "name": "Text columns with images",
    "class": "index-section",
    "max_blocks": 24,
    "settings": [
      {
        "type": "checkbox",
        "id": "title_enable",
        "label": "Show header",
        "default": true
      },
      {
        "type": "text",
        "id": "title",
        "label": "Heading",
        "default": "Text columns with images"
      }
    ],
    "blocks": [
      {
        "type": "text_block",
        "name": "Text",
        "settings": [
          {
            "type": "text",
            "id": "title",
            "label": "Heading",
            "default": "Add a title or tagline"
          },
          {
            "type": "image_picker",
            "id": "image",
            "label": "Image"
          },
          {
            "type": "radio",
            "id": "image_align",
            "label": "Image alignment",
            "options": [
              { "value": "top center", "label": "Top center" },
              { "value": "center center", "label": "Center" },
              { "value": "bottom center", "label": "Bottom center" }
            ],
            "default": "top center"
          }
        ]
      }
    ],
    "presets": [
      {
        "name": "Overlay Columns",
        "category": "Text",
        "blocks": [
          {
            "type": "text_block"
          },
          {
            "type": "text_block"
          },
          {
            "type": "text_block"
          }
        ]
      }
    ]
  }
{% endschema %} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到如下所示的内容:

[![](../Images/1dcc72fd9f4f46ef915d2ecbbbf303ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9ei2zN9s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://stayregular.net/content/2-blog/20180409-how-to-make-shopify-template-snippets/speakers.png)

## 易如图式

我希望这能帮助你为你的 Shopify 模板创建一些灵活的组件。这些可定制的模块使不太懂技术的编辑能够毫不费力地在新的水平上做出贡献。

有什么事情对你来说不正常吗？在推特上给我们打电话寻求帮助。或者[如果您需要 Shopify 的专业咨询和开发，今天就联系我们](http://stayregular.net/contact)。

奥斯卡保持常规

* * *

继续阅读:

*   [Shopify 片段上的文档](https://help.shopify.com/themes/liquid/tags/theme-tags#include)