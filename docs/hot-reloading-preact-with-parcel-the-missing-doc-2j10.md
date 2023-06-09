# 热重装预装包裹，丢失文件

> 原文：<https://dev.to/sadick/hot-reloading-preact-with-parcel-the-missing-doc-2j10>

今天我用[包裹](https://parceljs.org)作为捆绑者设置了一个 [preact](https://preactjs.com/) app。在我开始开发这个应用之前，我突然遇到了一个障碍。每当我对组件进行更改时，我的更改都会被附加到 html 文档中，而不是被替换。

[![gif](img/43be693de30df0366ef7e092af440d60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5HmjuEu6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gs5jnkudm77j9jb6t7xy.gif)

原来包裹不是问题所在，preact 才是。我像任何开发人员一样去看他们的文档。我发现他们的渲染默认附加到指定的节点。preact render 函数接受第三个参数，该参数应该是要替换的节点。

所以为了解决附加问题，我需要提供第三个参数。

```
import { h, render } from "preact";

const Happy = () => <div>Happy</div>;
const App = () => (
  <div>
    <Happy />
  </div>
);
const node = document.getElementById("root");

render(<App />, node, node.lastChild); 
```

Enter fullscreen mode Exit fullscreen mode

现在它的行为符合预期。也许包裹应该更新他们的文件包括这一点。我想提出一个拉取请求，但是他们的文档不在 github 上。

[![](img/3298225092459e98eb7aa3cb10327c39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RKpPoWh8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fw7bq21g5zsl5lznb0ky.gif)