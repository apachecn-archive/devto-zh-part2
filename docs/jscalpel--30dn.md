# Jscalpel 使操纵对象变得更加容易

> 原文：<https://dev.to/ihtml5/jscalpel--30dn>

[![](img/ca638a744b6ed58ecce364226af2d9f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nyIws4Em--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2g1eep5uzbw1jpobv6pk.png)

一个小的特征库，使得操作对象更加容易

## 概述

它很小但是非常有用，可以帮助你处理 javascript 原生对象。数据驱动的界面开发在今天非常普遍，我们在 [angular](https://github.com/angular/angular) 、 [react](http://www.github.com/facebook/react) 、 [vue](http://www.github.com/vuejs/vue) 中会遇到很多对象处理，包括设置默认值、查询、赋值等。， [jscalpel](http://www.github.com/ihtml5/jscalpel) 就是为这个场景而生的。

jscalpel 差一点，gzip 小于 **3k** ，一个你可以随时随地使用的库，不用担心任何事情。

## 文档

#### 查看文档请访问 https://ihtml5.github.io/jscalpel/

[Chinese document](https://ihtml5.github.io/jscalpel/index_zh.html)

## 安装

#### 使用 npm 安装

[![jscalpel](img/004440ebebdd4051de408f6057d54484.png)T2】](https://npmjs.org/package/jscalpel)

```
npm install jscalpel --save
yarn add jscalpel --save 
```

## 使用年限

#### Es6

```
 import Jscalpel from 'jscalpel' 
```

#### 包含在 html 中

```
 <script charset="utf-8" src="https://unpkg.com/jscalpel@latest/dist/index.js"></script> 
```

## 原料药

| 参数 | 类型 | 缺省值 | 使用 | 是必需的 | 所需版本 |
| --- | --- | --- | --- | --- | --- |
| 目标 | 字符串/对象 | {} | 目标 | 真实的 | 全部 |
| 深的 | 布尔型 | 错误的 | 是否深度复制目标对象 | 错误的 | 全部 |
| 前缀 | 线 | 不明确的 | 公共前缀，为密钥自动添加 | 错误的 | 全部 |
| 成功 | 功能 | 函数(){} | 分析成功时调用的函数 | 真实的 | ^0.6.2 |
| 错误 | 功能 | 函数(){} | 分析失败时调用的函数。 | 错误的 | ^0.6.2 |
| 小路 | 字符串/数组/函数 | [] | 小路 | 错误的 | ^0.6.2 |
| 插件 | 排列 | [] | 插件集，类似于 webpack 插件。 | 错误的 | ^0.6.2 |

## [在线试玩](https://jsfiddle.net/as3tLkdy/27/?utm_source=website&utm_medium=embed&utm_campaign=as3tLkdy)

## 代码

#### 1。简单模式

```
// mock data
var data = {
  status: '0',
  data: {
    response: {
      code: 1,
      msg: 'response msg'
    }
  }
}

// bind data
var jscalpelIns = jscalpel({
  target: data
})
jscalpelIns.get('data.response.code') // returned 1;
jscalpelIns.set('data.response.code', 12);
jscalpelIns.set({
  'status': '1'
})
jscalpelIns.get('data.response.code') // returned 12
jscalpelIns.get('status') // returned 1
jscalpelIns.has('data.response.code') // returned true

jscalpelIns.del('data.reponse.code') 

jscalpelIns.get('data.reponse.code') // returned undefined;
jscalpelIns.has('data.reponse.code') // returned false; 
```

#### 2 .高级模式

```
const res = {
  data: {
    article: [{
      articleId: 0,
        title: 'jscalpel'
    }]
  },
  response: {
    code: '0',
    msg: 'success'
  }
}
jscalpel({
       target: res,
  path: ['data.article.0', 'response.msg'],
  success:  (article, msg) => {
    console.log('keys=>array=>output:', article, msg);
  }
})

jscalpel({
    target: res,
  path: 'response.msg',
  success:  (msg) => {
    console.log('keys=>string=>output:',msg);
  }
}); 
```

#### 3 .使用前缀

```
jscalpel({
    target: res,
  prefix: 'response',
  path: ['code', 'msg'],
  success:  (code, msg) => {
    console.log('prefix=>output:', code, msg);
  }
}) 
```

#### 4 .动态路径

```
jscalpel({
  target: res,
  path: () => ['code', 'msg'].map((key) => `response.${key}`),
  success:  (code, msg) => {
    console.log('dynamic=>output:', code, msg);
  }
})

jscalpel({
    target: res,
  deep: true,
  prefix: 'response',
  path: ['code', 'msg'],
  success:  (code, msg, finalRes, keys) => {
    console.log( finalRes === res);
    console.log('deep into callback:', code, msg, finalRes, keys);
  }
}); 
```

#### 5 .使用插件

```
const logicMap = {
  'code': {
    match: ({value, name}) => value === '0',
    success: ({value, name}) => {
      console.log('logicPlugin', value, name);
    }
  }
}
jscalpel({
  target: res,
  deep: true,
  prefix: 'response',
  path: ['code', 'msg'],
  plugins: [jscalpel.jscalpelType, jscalpel.jscalpelLogic(logicMap)],
  success: (code, msg, finalRes, keys) => {
    console.log( finalRes === res);
    console.log('deep into callback:', code, msg, finalRes, keys);
  }
}) 
```

## 相关项目

[jscalpel 形式](https://github.com/ihtml5/jscalpel-orm)

从一个对象中提取所需的字段来生成另一个对象非常方便。

## 变更日志

#### 2017.9.14

添加 jscalpelLogic 插件，减少 ifelse，使运行逻辑可配置

#### 2018.3.08

给我欧姆

## 执照

[麻省理工学院许可证](https://opensource.org/licenses/MIT)。