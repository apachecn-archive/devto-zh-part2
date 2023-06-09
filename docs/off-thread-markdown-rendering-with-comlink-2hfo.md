# 使用 Comlink 进行离线标记渲染

> 原文：<https://dev.to/mizchi/off-thread-markdown-rendering-with-comlink-2hfo>

演示[https://markdown-buffer.netlify.com/](https://markdown-buffer.netlify.com/)

## TL；博士；医生

用 com link([https://github.com/GoogleChromeLabs/comlink](https://github.com/GoogleChromeLabs/comlink))
在 web worker 中备注

```
yarn add remark remark-html comlinkjs 
```

(我使用 package-bundler 来构建)

## 职工一方

src/worker.js

```
import remark from "remark";
import html from "remark-html";
import * as Comlink from 'comlinkjs'

const processor = remark().use(html)

class MarkdownCompiler {
  compile(raw) {
    return processor.processSync(raw).toString();
  }
}

Comlink.expose(MarkdownCompiler, self); 
```

## 主线程

src/index.js

```
import * as Comlink from 'comlinkjs'

const MarkdownCompiler = Comlink.proxy(new Worker("./worker.js"));

const main = async () => {
  const compiler = await new MarkdownCompiler();
  const result = await compiler.compile('**your markdown here**');
  console.log(result)
}
main(); 
```

Comlink 隐藏邮件以方便工作人员