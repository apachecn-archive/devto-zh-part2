# 使用 Comlink 在 Web Worker 中检测条形码

> 原文：<https://dev.to/chromiumdev/barcode-detection-in-a-web-worker-using-comlink-2mfd>

我是二维码的忠实粉丝，它们是现实世界和数字世界之间交换数据的非常简单和整洁的方式。几年来，我一直有一个名为 [QRSnapper](https://qrsnapper.com) 的小项目——它有几个名字，但这是我选定的一个——它使用`getUserMedia` API 从用户的相机中获取实时数据，以便它可以近乎实时地扫描二维码。

该应用程序的目标是保持 60fps 的用户界面和接近即时的二维码检测，这意味着我必须将检测代码放入一个 Web Worker(相当标准的东西)。在这篇文章中，我只想快速分享我是如何使用 [comlink](https://github.com/GoogleChromeLabs/comlink) 来大规模简化 Worker 中的逻辑的。

#### qrrclient . js

```
import * as Comlink from './comlink.js';

const proxy = Comlink.proxy(new Worker('/scripts/qrworker.js')); 

export const decode = async function (context) {
  try {
    let canvas = context.canvas;
    let width = canvas.width;
    let height = canvas.height;
    let imageData = context.getImageData(0, 0, width, height);
    return await proxy.detectUrl(width, height, imageData);
  } catch (err) {
    console.log(err);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### qrworker.js(网络工作者)

```
import * as Comlink from './comlink.js';
import {qrcode} from './qrcode.js';

// Use the native API's
let nativeDetector = async (width, height, imageData) => {
  try {
    let barcodeDetector = new BarcodeDetector();
    let barcodes = await barcodeDetector.detect(imageData);
    // return the first barcode.
    if (barcodes.length > 0) {
      return barcodes[0].rawValue;
    }
  } catch(err) {
    detector = workerDetector;
  }
};

// Use the polyfil
let workerDetector = async (width, height, imageData) => {
  try {
    return qrcode.decode(width, height, imageData);
  } catch (err) {
    // the library throws an excpetion when there are no qrcodes.
    return;
  }
}

let detectUrl = async (width, height, imageData) => {
  return detector(width, height, imageData);
};

let detector = ('BarcodeDetector' in self) ? nativeDetector : workerDetector;
// Expose the API to the client pages.
Comlink.expose({detectUrl}, self); 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢 Comlink，我认为它是库的游戏改变者，尤其是在创建跨线程工作的习惯性 JavaScript 时。最后，这里的一个巧妙之处是，本机条形码检测 API 可以在 worker 内部运行，因此所有逻辑都封装在 UI 之外。

[阅读全文](https://github.com/PaulKinlan/qrcode/blob/production/app/scripts/qrworker.js)。