# 使用谷歌云翻译和 Hugo 翻译博客

> 原文：<https://dev.to/chromiumdev/translating-a-blog-using-google-cloud-translate-and-hugo-1n9e>

我最近刚从印度回来，参加了 Google4India 的活动(很快会有报道)并会见了很多企业和开发者。讨论的最有趣的变化之一是推动更多以该国用户语言编写的内容，这在谷歌的所有产品中尤为明显，从更容易以用户语言搜索、查找内容，到以文本或语音形式向用户朗读内容。

整个旅程让我开始思考。我的博客是和雨果一起建的。Hugo 现在支持用多种语言编写的内容。Hugo 是完全静态的，所以创建新内容就是创建一个新文件，让构建系统发挥它的魔力。因此，也许我可以通过翻译工具运行我的静态内容来构建一些东西，让更多的人可以使用我的内容，因为人工翻译内容非常昂贵。

在我飞回英国的几个小时前，我创建了一个小脚本，它将把我的减价文件通过 [Google Cloud Translate](https://cloud.google.com/translate/) 运行，以创建页面的快速翻译，然后我可以快速托管它们。整个解决方案如下所示。这是一个相对基本的处理器，它忽略了 Hugo 序言，它忽略了“代码”,它忽略了引用——我的假设是，这些总是意味着保留它们被编写的方式。

注意:看起来我们的翻译学习软件在使用，所以[标记你的页面很重要，这样学习工具就不会使用谷歌翻译的内容作为算法的输入](https://cloud.google.com/translate/markup)。

```
// Imports the Google Cloud client library
const Translate = require('@google-cloud/translate');
const program = require('commander');
const fs = require('fs');
const path = require('path');

program
  .version('0.1.0')
  .option('-s, --source [path]', 'Add in the source file.')
  .option('-t, --target [lang]', 'Add target language.')
  .parse(process.argv);

// Creates a client
const translate = new Translate({
  projectId: 'html5rocks-hrd'
});

const options = {
  to: program.target,
};

async function translateLines(text) {
  if(text === ' ') return ' ';
  const output = [];
  let results = await translate.translate(text, options);

  let translations = results[0];
  translations = Array.isArray(translations)
    ? translations
    : [translations];

  translations.forEach((translation, i) => {
    output.push(translation)
  });

  return output.join('\n');
};

// Translates the text into the target language. "text" can be a string for
// translating a single piece of text, or an array of strings for translating
// multiple texts.
(async function (filePath, target) {

  const text = fs.readFileSync(filePath, 'utf8');

  const lines = text.split('\n');
  let translateBlock = [];
  const output = [];

  let inHeader = false;
  let inCode = false;
  let inQuote = false;
  for (const line of lines) {
    // Don't translate preampble
    if (line.startsWith('---') && inHeader) { inHeader = false; output.push(line); continue; }
    if (line.startsWith('---')) { inHeader = true; output.push(line); continue; }
    if (inHeader) { output.push(line); continue; }

    // Don't translate code
    if (line.startsWith('```

') && inCode) { inCode = false; output.push(line); continue; }
    if (line.startsWith('

```')) { inCode = true; output.push(await translateLines(translateBlock.join(' '))); translateBlock = []; output.push(line); continue; }
    if (inCode) { output.push(line); continue; }

    // Dont translate quotes
    if (inQuote && line.startsWith('>') === false) { inQuote = false; }
    if (line.startsWith('>')) { inQuote = true; output.push(await translateLines(translateBlock.join(' '))); translateBlock = []; output.push(line); }
    if (inQuote) { output.push(line); continue; }

    if (line.charAt(0) === '\n' || line.length === 0) { output.push(await translateLines(translateBlock.join(' '))); output.push(line); translateBlock = []; continue;} 

    translateBlock.push(line);
  }

  if(translateBlock.length > 0) output.push(await translateLines(translateBlock.join(' ')))

  const result = output.join('\n');
  const newFileName = path.parse(filePath);
  fs.writeFileSync(`content/${newFileName.name}.${target}${newFileName.ext}`, result);

})(program.source, program.target); 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，我对这个过程很满意。我知道机器翻译并不完美，但我的想法是，我可以增加我的内容对那些可能用他们自己的语言而不是英语搜索的人的影响，我可以增加我的内容的发现表面积，并希望帮助更多的人。

这将需要一段时间来看看这是否真的有助于人们，所以当我有更多的数据时，我会回来报告…现在在我的网站上运行我的脚本:)