# 使用 WebExtensions API - 101 的跨浏览器扩展

> 原文：<https://dev.to/vinceumo/cross-browser-extensions-with-webextensions-api---101-40hj>

这篇笔记最初发布在我的 [DevNotes](https://vinceumo.github.io/devNotes/javascript/2018/10/23/webextensionapi.html) 上

* * *

浏览器扩展为浏览器增加了特性和功能。它使用 HTML、CSS 和 JS 等 web 技术。

## [T1】manifest . JSON](#manifestjson)

浏览器扩展需要的唯一文件是`manifest.json`文件。

> 使用 manifest.json，可以指定关于扩展的基本元数据，比如名称和版本，还可以指定扩展功能的各个方面，比如后台脚本、内容脚本和浏览器操作。
> [MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json)

manifest.json 键依赖于您正在开发的扩展。

您可以在 [manifest.json - MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json) 上找到可用的密钥

### 举例:

```
{  //Mandatory  "manifest_version":  2,  "name":  "Name of your extension",  "version":  "1.0.0",  //  Recommended  "default_locale":  "en",  "description":  "Extension description",  "author":  "Your Name",  "homepage_url":  "https://github.com/vinceumo",  "icons":  {  "16":  "icons/icon16.png",  "32":  "icons/icon32.png",  "48":  "icons/icon48.png",  "128":  "icons/icon128.png"  }  } 
```

## 在本地添加和调试浏览器扩展

### 火狐

#### 添加分机

*   导航至`about:debugging`
*   勾选**启用插件调试**
*   点击**加载临时附件**
*   选择 *manifest.json* 文件

#### 调试扩展

*   导航至`about:debugging`
*   查找您的分机
*   点击**调试**
*   开发商收费将开放

### 铬/歌剧

#### 添加分机

*   导航至`chrome://extensions/`
*   点击**加载解包**
*   选择扩展文件夹

#### 调试扩展

加载软件包时，会显示后台脚本错误。

*   导航至`chrome://extensions/`
*   查找您的分机
*   点击**错误**按钮(只有出现错误时才会显示)
*   点击`View in Developer Tools`

调试弹出扩展:

*   打开弹出窗口
*   右键单击弹出窗口
*   点击检查

[Chrome 调试扩展](https://developer.chrome.com/extensions/tut_debugging)

### 边缘

#### 添加分机

*   导航至`about:flags`
*   勾选**启用扩展开发者功能**
*   点击**设置和更多的**图标(右边的 3 点图标)
*   从该菜单中选择**扩展**
*   点击**加载扩展**
*   选择扩展文件夹

#### 调试扩展

调试弹出扩展:

*   打开弹出窗口
*   右键单击弹出窗口
*   点击检查元素

[微软边缘调试扩展](https://docs.microsoft.com/en-us/microsoft-edge/extensions/guides/debugging-extensions)

## 使用聚合填充的跨浏览器扩展

Mozilla/web extension-poly fill 允许浏览器使用基于 Promise 的 WebExtension APIs。有些浏览器的风格可能需要一些调整。

请注意，opera 使用 Chromium，Vivaldi 和 Brave 也使用 Chromium，如果您的扩展可以在 Chrome 中工作，那么它可以在所有这些浏览器中工作。

## 发布扩展

### 铬/勇敢/维瓦尔第

Brave 和 Vivaldi 可以从 Chrome 网上商店下载扩展。

*   为您的扩展创建一个 zip 文件(清单文件需要在这个文件的根目录下)
*   在 [Google developer dashboard](https://chrome.google.com/webstore/developer/dashboard) 上连接/创建帐户
    *   如果你第一次提交延期，你需要支付 5 美元的费用
*   上传 zip 文件

### 火狐

*   为您的扩展创建一个 zip 文件(清单文件需要在这个文件的根目录下)
*   在[附加开发中心](https://addons.mozilla.org/en-US/developers/)上连接/创建帐户
*   提交新的加载项

### 歌剧

*   为您的扩展创建一个 zip 文件(清单文件需要在这个文件的根目录下)
*   在 [opera 附加平台](https://addons.opera.com/developer/)上连接/创建账户
*   提交新的加载项
*   你将需要等待适度，可能需要几个星期/几个月

## 弹出扩展的例子

我最近开发了浏览器扩展[A11Y-色盲-同理心-测试-浏览器扩展](https://github.com/vinceumo/A11Y-Color-Blindness-Empathy-Test-browser-extensions)。

这个扩展是一个弹出扩展，它注入了一个内嵌的 SVG 来过滤颜色、一些样式，并根据选中的单选按钮来改变当前打开的标签的类别。

该扩展将遵循以下 API 和结构:

*   [manifest.json -权限](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/permissions)
*   [清单. json -浏览器 _ 动作](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/browser_action)
*   [WebExtensions API - tabs](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/tabs)
*   [内容脚本](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts)

```
WebExtension
|   manifest.json // Give permissions to the active Tab and reference the popup `choose_test.html`
|
+---content_scripts
|       runTest.js // Script interacting with the active tab
|
+---icons
|       icon128.png
|       icon16.png
|       icon32.png
|       icon48.png
|
+---polyfills
|       browser-polyfill.js // Polyfill for chrome and opera
|
\---popup
        choose_test.css // Style of the popup
        choose_test.html // Markup of the popup
        choose_test.js // Script of the popup, click handler 
```

## 资源

*   [MDN 浏览器扩展](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions)
*   [Chrome 浏览器扩展](https://developer.chrome.com/extensions)
*   [微软 Edge 扩展 API 路线图](https://docs.microsoft.com/en-us/microsoft-edge/extensions/api-support/extension-api-roadmap)
*   [W3C 浏览器扩展](https://www.w3.org/community/browserext/)
*   [web extensions-examples-Github](https://github.com/mdn/webextensions-examples)
*   [如何使用 JavaScript 和浏览器 API 制作跨浏览器扩展- Medium](https://medium.freecodecamp.org/how-to-make-a-cross-browser-extension-using-javascript-and-browser-apis-355c001cebba)