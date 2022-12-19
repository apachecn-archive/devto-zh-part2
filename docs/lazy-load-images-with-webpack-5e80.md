# 用 Webpack 延迟加载图像

> 原文：<https://dev.to/pldg/lazy-load-images-with-webpack-5e80>

使用 [require.context](https://github.com/pldg/learn-webpack/tree/master/require-context) 自动包含文件夹中的所有图像，并仅在需要时使用[动态导入](https://github.com/pldg/learn-webpack/tree/master/dynamic-import)加载它们。优化你的站点(或应用)带宽、加载时间和缓存的好方法。

## 源代码

[learn-web pack/lazy-load-multi-images](https://github.com/pldg/learn-webpack/tree/master/lazy-load-multiple-images)

## 如何

### 懒导入一张图片

由于[文件加载器](https://github.com/webpack-contrib/file-loader)解析图像并返回其解析路径，我们可以将图像作为模块导入。

创建一个函数来动态 [import()](https://webpack.js.org/guides/code-splitting/#dynamic-imports) 一个图像，返回其解析后的路径并将其设置为`<img>` `src`属性。

```
const lazyLoadImage = (imageName, img) => {
  import(
    /* webpackMode: "lazy-once" */
    img/${imageName}`
  )
  .then(src => img.src = src.default)
  .catch(err => console.error(err));
};

export default lazyLoadImage; 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，动态导入为每个`imageName`创建一个新的块，相反，我们希望为所有图像创建一个单独的块。为此，我们使用了`/* webpackMode: "lazy-once" */`，它生成了一个单一的可惰性加载的块，可以满足对`import()`的所有调用。这有助于在调用图像时防止额外和不必要的网络请求。阅读网络包模式的[列表了解更多信息。](https://webpack.js.org/api/module-methods/#import-)

### 生成一个图像元素

创建一个函数来生成一个`<img>`元素，将它附加到一个 dom 容器中，并运行`lazyLoadImage`函数来设置它的`src`属性。

```
import lazyLoadImage from './lazyLoadImage';

const generateImage = (container, imageName) => {
  const img = document.createElement('img');
  container.appendChild(img);

  lazyLoadImage(imageName, img);
};

export default generateImage; 
```

Enter fullscreen mode Exit fullscreen mode

### 获取图片名称

我们需要让每个`imageName`在`lazyLoadImage`函数中使用它们。使用 [require.contex](https://webpack.js.org/guides/dependency-management/#context-module-api) 从`./images`文件夹中导入所有的`.jpg`图像，并且只匹配它们的扩展名。

```
const getImagesNames = () => {
  const r = require.context('./images', false, /\.jpg$/);

  // return an array list of filenames (with extension)
  const importAll = (r) => r.keys().map(file => file.match(/[^\/]+$/)[0]);

  return importAll(r);
};

export default getImagesNames; 
```

Enter fullscreen mode Exit fullscreen mode

### 生成所有图像

创建一个名为 *images.js* 的 javascript 文件，该文件在导入时将延迟加载并生成所有图像。

```
import getImagesNames from './getImagesNames';
import generateImage from './generateImage';

const images = document.querySelector('#images');
const imagesNames = getImagesNames();

// generate <img> element
// lazy-load each image and set its src attribute
// append <img> to #images container
imagesNames.forEach(name => generateImage(images, name)); 
```

Enter fullscreen mode Exit fullscreen mode

### 懒加载所有图片

您现在可以延迟导入 *images.js* 以在需要时一次生成所有图像。

```
if (somethingHappen) import(
  /* webpackPrefetch: true */
  './images'
); 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`webpackPrefetch: true`，到 lazy-chunk 的链接将被放置在`<head>`标签中。当浏览器空闲时，下载预取的块，并存储在浏览器缓存中以备后用。

```
<link rel="prefetch" as="script" href="0.js"> 
```

Enter fullscreen mode Exit fullscreen mode

更多信息请参见[预取-预加载-模块](https://webpack.js.org/guides/code-splitting/#prefetching-preloading-modules)和[link-rel-prefetch-preload-in-web pack](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c)和[preload-prefetch-and-priorities-in-chrome](https://medium.com/reloading/preload-prefetch-and-priorities-in-chrome-776165961bbf)。

## 备注

查看应用程序时，检查 devtools 网络选项卡。确保不要禁用 devtools 中的缓存，否则将无法从缓存中提供预取的块。

如果想在主包中包含 *images.js* ，可以添加`webpackMode: "eager"`而不是`webpackPrefetch: true`。

这篇文章是我的[learn-web pack](https://github.com/pldg/learn-webpack)repositories 集合的一部分。

## 进一步阅读

*   [码分](https://survivejs.com/webpack/building/code-splitting/)按 juho 的内乡
*   大卫·吉尔伯森的《100 种用网络包分割你的块的正确方法》
*   鲁本斯·皮涅罗·贡萨尔维斯·卡瓦尔坎特