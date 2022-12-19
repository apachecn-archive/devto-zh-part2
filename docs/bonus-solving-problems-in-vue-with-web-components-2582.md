# 额外收获:用 Web 组件解决 Vue 中的问题

> 原文：<https://dev.to/bennypowers/bonus-solving-problems-in-vue-with-web-components-2582>

这是我正在做的关于 web 组件的系列文章中的一篇附加文章。

1.  [第 1 部分:标准](https://dev.to/bennypowers/lets-build-web-components-part-1-the-standards-3e85)
2.  [第二部分:多孔填料](https://dev.to/bennypowers/lets-build-web-components-part-2-the-polyfills-dkh)
3.  [第 3 部分:普通成分](https://dev.to/bennypowers/lets-build-web-components-part-3-vanilla-components-4on3)

关于聚合物库的第 4 部分即将完成。在我们等待的时候，请注意一位学生向我提出的这个巧妙的问题，我们可以用 web 标准来解决它:

他们使用一个库在 Vue 组件中呈现 WebGL globe。他们想生成一组标记，然后跟踪哪些标记被打开，哪些被关闭。WebGL 库提供了一些 API，用于将一串`innerHTML`附加到每个标记的弹出窗口，但是没有公开任何 API 来跟踪打开、关闭或点击事件。

我有一点邪恶的想法😈。如果我们不能在库弹出窗口中添加行为，但是我们可以添加 HTML，那么如果我们添加封装其自身行为的 HTML 会怎么样呢？

**🎩Web 组件拯救世界！！👨‍💻**

## 定义`<popup-tooltip>`

我们需要的是一个 HTML 元素，它在每次包含弹出窗口打开或关闭时触发一个事件。WebGL 库使用`style="visibility: visible"`来打开和关闭弹出窗口，所以我们将创建一个使用`MutationObserver`来观察它自己的父元素的元素。

```
class PopupTooltip extends HTMLElement {
  constructor() {
    super();
    this.observerCallback = this.observerCallback.bind(this);
    this.attachShadow({mode: 'open'});
    this.shadowRoot.appendChild(document.createElement('slot'));
    this.observer = new MutationObserver(this.observerCallback);
  }

  connectedCallback() {
    // HACK: WebGL library toggles style.visibility on it's own
    // generated DOM to hide and show tooltips.
    const libraryContainer = this.parentElement.parentElement.parentElement;
    const config = { attributes: true, subtree: false, children: false };
    this.observer.observe(libraryContainer, config);
  }

  observerCallback([{target}]) {
    const visible = target.style.visibility === 'visible';
    const type = 'popup-' + visible ? 'opened' : 'closed';
    const bubbles = true;
    const composed = true;
    const detail = this;
    this.dispatchEvent(new CustomEvent(type, { bubbles, composed, detail }));
  }
}

customElements.define('popup-tooltip', PopupTooltip); 
```

Enter fullscreen mode Exit fullscreen mode

## 连接到 Vue 包装器

所以现在我们有了一个`<popup-tooltip>`元素，每当它的容器的可见性被 WebGL 库切换时，它将触发一个`popup-opened`或`popup-closed`事件。我们在包装 Vue 组件的私有 DOM 中设置监听器:

```
<!-- WebGL lib instanciates on '#earth' -->
<div id="earth" @popup-opened="onPopupOpened" @popup-closed="onPopupClosed"></div> 
```

Enter fullscreen mode Exit fullscreen mode

## 创建每个弹出窗口

然后，当我们实例化 WebGL 库并传递我们的数据时，我们设置了标记以在其工具提示内容中显示一个`<popup-tooltip>`元素。

```
geoMarkers.forEach(marker => {
  const location = marker.latLng;
  const glMarker = new WebGLLib.popup({/*...*/});
  // NB: popupHTML is **just HTML**, there's no framework or library here.
  const popupHTML = `<popup-tooltip data-location="${location}">${marker.title}</popup-tooltip>`;
  // `bindPopup` is a method on our WebGL library's marker API.
  glMarker.bindPopup(popupHTML, config);
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 盈利！

我们要做的最后一件事是跟踪哪些弹出窗口打开了，哪些关闭了。

```
onPopupOpened({target: {dataset: {location}}}) {
  const [lat, lng] = location.split(',');
  console.log(`opened: lat: ${lat} lng: ${lng}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

你不需要放弃你的框架来使用 web 组件。您可以在任何可以使用 HTML 和 JavaScript 的地方使用它们。这正是 web 组件在这里成功的原因:我们的 GL 库没有将 Vue 组件作为输入，而是采用了一个 HTML 字符串。

几天后，我们将在聚合物库的第 4 部分再见。

您想就此处涉及的任何主题进行一对一的辅导吗？[![Contact me on Codementor](../Images/97f0e0737ce864c47f2412396b01e737.png)T2】](https://www.codementor.io/bennyp?utm_source=github&utm_medium=button&utm_term=bennyp&utm_campaign=github)