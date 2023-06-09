# 一起使用 CSS 滤镜和 CSS 变量或@关键帧

> 原文：<https://dev.to/desolosubhumus/using-css-filters-and-either-css-variables-or-keyframes-together-28cl>

# 使用 CSS 滤镜和 CSS 变量或@keyframes 来制作不同的样式

这一切都始于一本个人食谱。我想为每个食谱创建一个 HTML 文件，为一个菜单创建另一个文件，以便像传统印刷食谱中的目录一样浏览整个菜单。这可能有点混乱，因为它只是本地文件，但是如果我想为不同类型的食谱创建单独的样式，我可能很快就会得到一堆难以管理的样式表。

当然，一旦我用过滤器解决了这个问题，我就决定用我新创建的补丁进行试验，看看我还能用它做些什么。事实证明，我可以轻而易举地做出重大改变。我让 CSS 过滤器改变每个元素相对于背景色的颜色，用 CSS 变量(和一点 JavaScript)进行实验，使用一种十六进制颜色(rgb、hsl 和命名颜色也可以)为不同的页面分配不同的背景色，甚至通过添加一点@keyframes API 来使整个配色方案具有动画效果。也就是说，这仍然比我通常推荐的要花费更多的浏览器处理时间，因为我是一个坚持加载速度的人，所以尽量少用这些技巧。

## 滤镜

让浏览器计算网站的整个调色板的一个简单方法是使用过滤器。这比不上使用流行的预制调色板进行设计，或者根据流行趋势或你自己丰富多彩的创新设计出新的调色板，但这在数学上是有效的。例如，假设您想使用单色配色方案创建一个调色板。你可以盯着一个色轮，挑选出同一种颜色的多种色调、色彩和色调，你可以使用像 [ColorHexa](https://www.colorhexa.com) 这样的网站，或者你可以让浏览器使用过滤器来找出它。

```
 body{background:#9eb89e;}
    /*the body background color is a gray-green tone, almost sage*/

div{border:3px solid #dfe7df;}
    /*the div border is a pale mint*/

p{border:3px solid #353d35;}
    /*the paragraph border is a dark forest green*/ 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
 body{background:#9eb89e;}
    /*the body background color is a gray-green tone, almost sage*/

div{filter:invert(100%);border:3px solid currentColor;}
    /*the div border is a pale mint*/

p{filter:brightness(10%);border:3px solid currentColor;}
    /*the paragraph border is a dark forest green*/ 
```

Enter fullscreen mode Exit fullscreen mode

一旦样式表中除主色之外的所有颜色都由过滤器定义，并根据主色计算新的颜色，那么更改整个配色方案就很简单了，假设您坚持使用同一类型的配色方案。如果你计划从一个单色到一个三色配色方案，这将不会很好地工作，但是它对于从一个单色方案到另一个单色方案是完美的。此时，您可以添加一个样式切换器，允许用户使用单个变量控制整个配色方案。

## 变量

要将它设置为一个风格切换器，您需要一点 JavaScript。在这个选项中，您将为每个配色方案设置单击按钮，但是修改它以接受来自表单的输入也同样有效。这只是一个例子。

```
 const root = document.documentElement 
const themeBtns = document.querySelectorAll('.theme > button')

themeBtns.forEach((btn) => {
  btn.addEventListener('click', handleThemeUpdate)
})
    /*this creates the ability to control styles with buttons using the click events*/

function handleThemeUpdate(e) {
  switch(e.target.value) {
    case 'sage': 
      root.style.setProperty('--main', '#9eb89e')
      break
    case 'wetsand': 
      root.style.setProperty('--main', '#b8b89e')
      break
  }
}
    /*this is where we add the styling that changes by way of CSS variables*/ 
```

Enter fullscreen mode Exit fullscreen mode

然后你所要做的就是引用 HTML 中的脚本，将类“theme”添加到样式切换器按钮的包含元素中，将值和 id“sage”和“wetsand”添加到按钮中，然后你就可以使用你的变量了。

```
 body{background:var(--main);}
    /*the body background color is a gray-green tone, almost sage*/

div{filter:invert(100%);border:3px solid currentColor;}
    /*the div border is a pale mint*/

p{filter:brightness(10%);border:3px solid currentColor;}
    /*the paragraph border is a dark forest green*/ 
```

Enter fullscreen mode Exit fullscreen mode

现在，只需点击一个按钮，您的用户就可以使用 JavaScript 更改一种颜色，从一种配色方案切换到另一种配色方案，进而使用滤镜以数学方式控制所有其他颜色。不过，我们可以做出不同的改变，让整个过程充满活力。

## 用@keyframes API 制作动画

对于自动改变的配色方案，跳过上面的 CSS 变量和脚本，添加一个@keyframes 动画来控制主色。

```
 body{background:#9eb89e;}
    /*the body background color is a gray-green tone, almost sage, as a fallback*/
    /*@keyframes will animate this color scheme in browsers that support it*/

div{filter:invert(100%);border:13px solid currentColor;}
    /*the div border is a paler tint of the body background color*/

p{filter:brightness(10%);border:13px solid currentColor;}
    /*the paragraph border is a darker shade of the body background color */

@keyframes colorSwitch{
0%{background:#9eb89e;}
25%{background:#b8b89e;}
50%{background:#b89eb8;}
75%{background:#9e9eb8;}
100%{background:#9eb89e;}}
body{animation-name:colorSwitch;animation-duration:15s;animation-iteration-count:infinite;}
    /*the main body color will change over the course of 15 seconds and then repeat */ 
```

Enter fullscreen mode Exit fullscreen mode

现在，整个风格将通过四种不同的单色配色方案无限循环。为了在从一个到下一个的过程中避免不和谐，我选择使用四色配色来定义四种主色。

更新:我创建了一个要点来演示在 CSS 中仅使用一种指定的颜色来计算整个配色方案时如何使用过滤器。请原谅例子中过于花哨的颜色；它们用于演示滤镜如何工作，*而不是*作为一组建议的配色方案。