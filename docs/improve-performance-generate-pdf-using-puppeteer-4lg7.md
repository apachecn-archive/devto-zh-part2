# 提高性能使用木偶师生成 pdf

> 原文：<https://dev.to/chuongtrh/improve-performance-generate-pdf-using-puppeteer-4lg7>

我的第一篇关于使用木偶师生成 pdf 的帖子在[这里](https://dev.to/chuongtrh/generate-simple-pdf-from-html-41i0)。

在这篇文章中，我想分享如何提高 pdf 生成性能的技巧。

我们可以使用 [page.setContent](%28https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pagesetcontenthtml%29) 来提高性能。

**使用转到**

```
console.time('launch')
const  browser  =  await  puppeteer.launch({
args: ['--no-sandbox'],
headless: true
});
console.timeEnd('launch');
console.time('newPage')
const  page  =  await  browser.newPage();
console.timeEnd('newPage')
console.time('goto')
await  page.goto(`data: text/html ,${finalHtml}`, {
waitUntil: 'networkidle0'
});
console.timeEnd('goto')
console.time('pdf')
await  page.pdf(options);
console.timeEnd('pdf') 
```

原木

```
launch: 168.766ms
newPage: 87.764ms
goto: 1018.925ms
pdf: 109.687ms 
```

**使用 setContent**

```
console.time('launch')
const  browser  =  await  puppeteer.launch({
args: ['--no-sandbox'],
headless: true
});
console.timeEnd('launch');
console.time('newPage')
const  page  =  await  browser.newPage();
console.timeEnd('newPage')
console.time('setContent')
await  page.setContent(finalHtml);
console.timeEnd('setContent')
console.time('pdf')
await  page.pdf(options);
console.timeEnd('pdf') 
```

原木

```
launch: 147.349ms
newPage: 89.669ms
setContent: 15.247ms
pdf: 161.551ms 
```

明白了，**设置内容**比**转到**快

感谢您阅读我的帖子。