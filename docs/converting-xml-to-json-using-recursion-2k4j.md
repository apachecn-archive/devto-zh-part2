# 使用递归将 XML 转换为 JSON

> 原文：<https://dev.to/niinpatel/converting-xml-to-json-using-recursion-2k4j>

有一天，我正在开发一个需要从第三方 rest api 获取数据的应用程序，接下来发生的事情是 JavaScript 开发人员最糟糕的噩梦之一。

服务器在中发回了响应..喘气..像任何正常的 rest api 那样使用 XML 而不是 JSON。

[![](img/d220d5d976165e2843cfc855a5fcc996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--efGGmIaG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXuRqFNT2fMExddcrERq-6g.jpeg)

因此，我想出了一种简单地将 XML 转换成 JavaScript 对象的方法。这是我试图读取的数据的一个例子。

请记住，这段代码使用了 WebAPIs，所以它在服务器端 javascript 中不可用，比如 NodeJS。这非常适合 React 或 Angular 等前端应用。

XML 的格式一般是这样的:

```
<book>
    Some title
    <description>some description </description>
    <author>
        <id>1</id>
        <name>some author name</name>
    </author>
    <review>nice book</review>
    <review>this book sucks</review>
    <review>amazing work</review>
</book> 
```

Enter fullscreen mode Exit fullscreen mode

我希望输出看起来像这样:

```
{
  "book": {
    "title": "Some title",
    "description": "some description",
    "author": { "id": "1", "name": "some author name" },
    "review": ["nice book", "this book sucks", "amazing work"]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为 XML 有很多嵌套标签，所以这个问题是递归实际应用的一个完美例子。

在我们开始编码之前，我们需要理解一个叫做 [DOMParser](https://developer.mozilla.org/en-US/docs/Web/API/DOMParser) Web API 的东西。

根据 MDN 文档，

> `DOMParser`接口提供了将 [XML](https://developer.mozilla.org/en-US/docs/Glossary/XML) 或 [HTML](https://developer.mozilla.org/en-US/docs/Glossary/HTML) 源代码从字符串解析到 DOM 树的能力。

简单地说，它将 XML 字符串转换成 DOM 树。它是这样工作的。

假设我们有一个存储在字符串 strxml 中的 XML。我们可以将其中的数据解析为 DOM 树，如下所示:

```
let strxml = `<book>Some title
<description>some description </description>
<author>
    <id>1</id>
    <name>some author name</name>
</author>
<review>nice book</review>
<review>this book sucks</review>
<review>amazing work</review></book>
`;

const parser = new DOMParser();  // initialize dom parser
const srcDOM = parser.parseFromString(strxml, "application/xml");  // convert dom string to dom tree. 

// Now we can call DOM methods like GetElementById, etc. on scrDOM. 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经掌握了基本知识。让我们开始写伪代码。

```
Initialize variable jsonResult is empty object. 
If scrDOM has no children nodes:
    return innerHTML of the DOM. // This is our base case.

For each childNode in children nodes:
    Check if childNode has siblings of same name. 
    If it has no siblings of same name: 
        set childnode name as key whose value is json of the child node. (we're calling the function recursively.)
    If it has no siblings of same name
        set childnode name as key whose value is an empty array, every child whose name is same as this pushed into this array.
return jsonResult 
```

Enter fullscreen mode Exit fullscreen mode

下面是 JavaScript 代码:

```
/**
 * This function coverts a DOM Tree into JavaScript Object. 
 * @param srcDOM: DOM Tree to be converted. 
 */
function xml2json(srcDOM) {
  let children = [...srcDOM.children];

  // base case for recursion. 
  if (!children.length) {
    return srcDOM.innerHTML
  }

  // initializing object to be returned. 
  let jsonResult = {};

  for (let child of children) {

    // checking is child has siblings of same name. 
    let childIsArray = children.filter(eachChild => eachChild.nodeName === child.nodeName).length > 1;

    // if child is array, save the values as array, else as strings. 
    if (childIsArray) {
      if (jsonResult[child.nodeName] === undefined) {
        jsonResult[child.nodeName] = [xml2json(child)];
      } else {
        jsonResult[child.nodeName].push(xml2json(child));
      }
    } else {
      jsonResult[child.nodeName] = xml2json(child);
    }
  }

  return jsonResult;
}

// testing the function
let xmlstr = `<book>Some title
<description>some description </description>
<author>
    <id>1</id>
    <name>some author name</name>
</author>
<review>nice book</review>
<review>this book sucks</review>
<review>amazing work</review></book>
`;

// converting to DOM Tree
const parser = new DOMParser();
const srcDOM = parser.parseFromString(xmlstr, "application/xml");

// Converting DOM Tree To JSON. 
console.log(xml2json(srcDOM));

/** The output will be
{
  "book": {
    "title": "Some title",
    "description": "some description",
    "author": { "id": "1", "name": "some author name" },
    "review": ["nice book", "this book sucks", "amazing work"]
  }
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这是将 XML 字符串转换成 JSON 对象的基本算法/代码。因为它使用递归，所以它可以深入 DOM 树并解析每个元素。

这适用于大多数情况。你可以根据自己的需要或者要求修改这个算法。