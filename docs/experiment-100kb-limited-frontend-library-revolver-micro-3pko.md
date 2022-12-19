# 实验:100kB 有限前端库旋转微

> 原文：<https://dev.to/xshiftx/experiment-100kb-limited-frontend-library-revolver-micro-3pko>

# 一个实验

对于我的小型 [CMS RevolveR](https://dev.to/xshiftx/writing-revolver-cms-just-for-fun-5efo) 来说，我需要一个前端库，它可以提取请求，并用 HTML 标记编辑器和 HTML 表单美化器的模块制作 3D CSS 动画。

[![RevolveR frontend interface improvements](../Images/de7f1eb31df1e8f4ce913bd074c8a46a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jeOIX6C2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nw3k6i6p5k5vlc4sprea.PNG)

所以。我把堆积如山的旧片段用相同的风格在 ES7 上重写了一遍。

# 什么是左轮手枪

前端 JavaScript 库 Revolver micro 是一个简单、强大和轻量级(只有 95Kb 的未压缩代码)的解决方案，不依赖于 DOM 元素及其属性，包括许多模块，可以使用标签、滑块、模态窗口、获取请求和其他东西。

## 连接脚本

转轮核心包括一个自动初始化模块。要运行文档下的库，您需要连接 bootstrap 并在#revolver 脚本标签中放置一个代码。示例:

`<script src="./app/revolver.js?prod=1.0.8" async id="revolver">
// charging weapons with namespace
const revolver = new Revolver('$');
// now you cant shout with $
let element = $.dom('.someclass:first-child');
</script>`

您可以使用任何名称空间来获得更好的代码风格。

## CSS 移动模式

旋转器自动从库中连接一个引导 css，并监听窗口大小的变化，将其切换到移动模式。

1.  mobile.css:小于 320px 到 1920px 的屏幕和手机浏览器 UA 的 schema

## 变量

库在自身名称空间中保留了一些变量:

`$.isM()` -如果手机浏览器此代码返回 true。

`$.sizes[]` -静态窗口尺寸包含【宽度，高度】。

`$.currentSizes[]` -自动及时刷新的实际文件尺寸包含【宽度，高度】。

`$.curxy`-x 和 y 语句的实际鼠标位置。

`$.curOffset` -偏移量 x 和 y 中的实际滚动位置

`$.startIndex` -第一次打开的页面标题(仅用于定位模块的正确工作)

## 位置 API

左轮手枪提供了一个正确的工作与浏览器的历史 API 使用位置模块。例如:

`$.location('Page Title','http://somedomain.com/index.html', function(){console.log(this)})`

你可以使用这个模块编写你自己的获取路由器来纠正页面标题和 url 的切换。这个模块并不意味着文件将被重新加载，它只是提供界面的变化。

## 表格样式

这个模块修饰了所有在核心表单中定义的元素，比如选择或标签。

`$.formStyler();`

## 文本区域的标记编辑器

这是一个简单的 HTML 标记编辑器，可以自动切换到表单中的所有文本区域，不包括基本的标记按钮。

`$.markupEditor();`

## DOM 引擎

很少有方法可以处理 DOM 元素。

`$.dom('selector')`返回一堆合适的 HTML 元素。可用的简单选择器，如`#obj .class tag`和前缀，用于查找文档中的最后一个和第一个元素- `'$.dom('#tabs .tabactive code:first-child')`或`$.dom('body p:last-child, ul li:first-child')`；DOM 引擎支持多重选择器。

要创建新的 DOM 元素，可以使用“new”指令，语法如下:

`$.dom('dfn',"new|before|footer:first-child", { html: '<a style="color:#b06400" href="#">version 0.5 alpha</a>', attr: { style: "color:#b06400; text-align:center; display:block" }`

要删除元素，您可以使用' del '指令:

`$.dom('.prost, div', "del");`

要在元素中插入 html 或文本数据，可以使用:

`$.insert($.dom('h1'),'<b>test</b>');`

要换行元素:
`$.dom('span', 'wrap', 'section')`

要展开元素:
`$.dom('section', 'unwrap',)`

替换:
`$.dom('span','replace','<div>hey</div>')`

### DOM 样式和属性

Revolver 可以设置 html 元素的属性或者使用样式属性进行操作。在设置、获取或删除属性的示例中，您需要使用以下语法:

`$.attr('h1', {'data-test': 'some value'});` -设置一个或多个属性。

`$.attr('h1', {'data-test': null});` -删除属性。

`$.attr('h1', 'data-test, style, etc');` -获取属性值。

要设计 CSS 样式，您可以使用:

`$.dom("html","style",['background:rgba(233,246,255,.8)','color:#111']);`

有一些操纵类属性的方法:

`$.addClass('#some','class-test');`

`$.removeClass('#some', 'class-test');`

`$.hasClass('#some', 'class1 class2 class3'); //returns true if all classes defined for element #some`

和

`$.toggleClass('#some', 'class-test');`

## 动画

动画引擎允许动画化一些 CSS 属性，如宽度、高度、颜色、边距、填充、字体大小和 CSS3 转换，如旋转、skewX、skewY、translateX、translateY 和 scale:

`$.dom("#mainContents","animate",['border-radius:25px 0px:100:pulse']);`

`$.dom("a:first-child", "animate", ['transform:rotate(360deg) scale(0.7):2000']);`

`$.dom("h1:first-child", "animate", ['color:brown:3000:pulse'])`

每个财产都有不同的持续时间和不同的原因。

可用效果:easeIn，easeOut，easeOutQuad，easeOutCubic，easeInQuart，easeOutQuart，easeOutQuart，easeOutQuart，easeInQuint，easeOutQuint，easeOutQuint，elastic，easeOutElastic，easeOutElastic，easeInSin，easeOutSin，easeOutSin，easeOutCirc，easeOutCirc，easeOutCirc，easeInQuad，easeInExpo，easeOutExpo，easeOutBounce，Bounce，bouncePast，bounce，激进，和声

### 显示和隐藏模块

ещ用高度动画旋转器显示和隐藏元素包含功能`$.hide()`和`$.show()`:

`$.show('#elem', 800);`

`$.hide('#elem', 1300);`

## 标签页模块

要设置标签等内容的样式:

`$.tabs('#tabs li', '#tabs div', function() { });`

## 旋转模块

旋转模块是一个简单的滑块。

`$.rotate('#presentation p', function(){}, 1500);`

## 模态框 API

要创建一个模态窗口，你可以使用 GrayBox API。

`$.modal('Get FE Revolver Micro','<a target="_blank" style="color:#b06400" href="./app/Revolver.js">version 1.5</a>',[500,200]);`

其中第一个参数是标题，第二个参数是内容，第三个参数包含大小数组[宽度，高度]。

## 提示 API

旨在显示包含已设置属性中的文本的提示。示例:

`$.hint('a', 'data-title');`

## 取模块

获取模块允许你的应用程序用不同的方法和不同类型的内容异步地接收内容。

`$.event('input[type="submit"]', 'click', function(e) {
e.preventDefault();
let data = new FormData();
let form = $.dom('#test input[type="text"]');
for(var j in form) {
data.append( form[j].name, form[j].value );
}
$.fetch('http://test:88/post.php','POST','text', data, function(){
$.log(this)
});
});`

## 获取提交模块

这个模块允许你使用 fetch 自动提交表单数据

`$.fetchSubmit('form', 'text', function(){
$.modal('Form API test :: sended variables', $.findHTMLByTag('pre', this)[0].outerHTML, [800, 300]);
});`

可用的方法 POST 和 GET。
可用内容类型文本，json。

第三个参数包含 POST 或 GET 查询变量。

## findHTMLByTag 模块

这个模块允许你通过使用标签或者 CSS 选择器在一些 HTML 内容中找到一些 HTML 内容。

`$.findHTMLByTag('#root', rootHTMLElement)`

## 存储模块

存储模块提供一个简单的 API 来使用本地存储。

`$.storage(['Revolver={"js library": "1.0.8"}'],'set');` -设定一个值。

`$.storage('data-test', 'get');` -获取值。

`$.storage(['data','testing'], 'del');` -删除数值。

## Cookie 模块

存储模块提供了一个简单的 API 来使用 cookies。

`$.cookie(['Revolver={"js library": "1.0.8"}'],'set');` -设定一个值。

`$.cookie('data-test', 'get');` -获取值。

`$.cookie(['data','testing'], 'del');` -删除数值。

## 外部 JS

要以异步模式连接外部 JS 文件，您可以使用 next API:

`$.externalJS('http://domain.com');`

## 滚动

您可以使用 api 对 targen 元素使用平滑滚动动画

`$.scroll() // sets position to top of page with smooth and opacity animation`

`$.scroll('#target_element') // set screen position to target element and animate opacity of target`

## 事件 API

事件 API 支持添加事件监听器的所有注册事件。添加点击事件的示例:

`$.click('aside h3', 'click', function(e){ e.preventDefault(); });` -点击事件；

## 火灾事件

事件 API 允许您使用 API 模拟点击或提交等事件:

`$.fireEvent('aside h3', 'click', function(e){ e.preventDefault() })`

## 帮手

Revolver core 包含了很多帮助工具，可以改进和简化你自己的模块和 API 的开发。明码就能看出来。

# 结论和来源

所以。作为一个实验，我能够用 95kB 的代码甚至更多的 jQuery 代码来夯实一切，并摆脱 UI 对 bootstrap 的依赖。

来源:GitHub 上的[左轮](https://github.com/xShiftx/RevolveR)。
项目地点: [CyberX](https://cyberx.pro/) 。