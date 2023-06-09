# 在 Web Worker 中使用 WASM 运行 FFMPEG

> 原文：<https://dev.to/chromiumdev/running-ffmpeg-with-wasm-in-a-web-worker-5gb5>

我喜欢[ffmpeg . JS](https://github.com/Kagami/ffmpeg.js)，这是一个用 asm.js 编译的简洁工具，它让我可以构建 JS web 应用程序，可以快速编辑视频。FFMPEG.js 还与 web workers 一起工作，这样您就可以在不阻塞主线程的情况下对视频进行编码。

我也喜欢 [Comlink](https://github.com/GoogleChromeLabs/comlink) 。Comlink 让我可以通过公开函数和类来轻松地与 web 工作者交互，而不必处理复杂的`postMessage`状态机。

我最近把两者结合在了一起。我正在[试验将 FFMPEG 导出到 Web Assembly](https://github.com/PaulKinlan/ffmpeg.js/tree/wasm) (它工作了——耶)，我想清理当前 FFMPEG.js 项目中所有的后期消息工作。下面是代码现在的样子——我认为它非常整洁。我们有一个导入 ffmpeg.js 和 comlink 的 worker，它只是公开 ffmpeg 接口，然后我们有一个加载 worker 的网页，然后使用 comlink 创建 ffmpeg API 的代理。

干净利落。

**worker.js**

```
importScripts('https://cdn.jsdelivr.net/npm/comlinkjs@3.0.2/umd/comlink.js');
importScripts('../ffmpeg-webm.js'); 
Comlink.expose(ffmpegjs, self); 
```

**client.html**

```
let ffmpegjs = await Comlink.proxy(worker);
let result = await ffmpegjs({ 
    arguments: ['-y','-i', file.name, 'output.webm'], 
    MEMFS: [{name: file.name, data: data}], 
    stdin: Comlink.proxyValue(() => {}), 
    onfilesready: Comlink.proxyValue((e) => { 
      let data = e.MEMFS[0].data; 
      output.src = URL.createObjectURL(new Blob([data]));
      console.log('ready', e) 
    }), 
    print: Comlink.proxyValue((data) => { 
      console.log(data); stdout += data + "\n"; 
    }), 
    printErr: Comlink.proxyValue((data) => { 
      console.log('error', data); stderr += data + "\n"; 
    }), 
    postRun: Comlink.proxyValue((result) => { 
     console.log('DONE', result); 
    }), 
    onExit: Comlink.proxyValue((code) => { 
     console.log("Process exited with code " + code); 
     console.log(stdout); 
    }),
  }); 
```

我真的很喜欢 Comlink，Workers 和 WASM 编译模块可以一起玩。我得到了与 WASM 模块直接交互的惯用 JavaScript，它在主线程之外运行。

[阅读全文](https://github.com/PaulKinlan/ffmpeg.js/blob/wasm/examples/async.html)。