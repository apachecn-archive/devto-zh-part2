# 使用 WebAssembly 尝试虚拟 dom 库“asm-dom”

> 原文：<https://dev.to/kwbtsts/try-asm-dom-that-is-virtual-dom-library-by-using-webassembly--1mli>

# “ASM-DOM”是什么？

[https://github.com/mbasso/asm-dom](https://github.com/mbasso/asm-dom)
ASM-dom 是一个使用 WebAssembly 的虚拟 DOM 库。它的虚拟 DOM 的“diff”和“patch”算法是由 WebAssembly 实现的，这是一个最小化的实现，所以它没有 React 那么高的功能。我希望通过 WebAssembly 可以非常快。

[初始提交的日期](https://github.com/mbasso/asm-dom/commit/1e619cab3895d846bf389ceb0c175f0b2f02292e)是 2017 年 2 月 26 日，所以是一年前。
包含[【snabbdom】](https://github.com/snabbdom/snabbdom)的名称。这可能与 snabbdom 有关。

> 西蒙·弗里斯·温杜姆，2015 年作为 snabbdom 项目的一部分
> 
> # 用法
> 
> 它可以用 C++写，但是这次我用了 JS，比如 WEB。

```
npm i --save asm-dom 
```

```
npm i -D arraybuffer-loader 
```

我通过 webpack 添加了`webpack`和`webpack-dev-server`进行捆绑。

```
npm i -D webpack webpack-dev-server 
```

`webpack.config`是这样的。我添加了`arraybuffer-loader`来加载`.wasm`文件。我参考[JS](https://github.com/mbasso/asm-dom/blob/master/docs/js.md)的文件。

```
var resolve = require('path').resolve;

module.exports = env => {
  const dist = env.prod ? 'docs' : 'dist';
  return {
    entry: './src/index.js',
    output: {
      filename: 'bundle.js',
      path: resolve(__dirname, dist),
      pathinfo: !env.prod,
    },
    node: {
      fs: 'empty'
    },
    devtool: env.prod ? 'source-map' : 'eval',
    devServer: {
      contentBase: resolve(__dirname, dist),
      historyApiFallback: true,
      compress: true,
      port: 9000
    },
    module: {
      loaders: [
        {
          test: /\.wasm$/,
          loaders: ['arraybuffer-loader']
        }
      ]
    }
  };
} 
```

# VS“虚拟世界”

我比较了“asm-dom”和虚拟 dom 库“虚拟 DOM”的速度。我测了一下替换`<div>`的 30000 个文本元素的时间。我用的是 Chrome ver61。

# “ASM-DOM”胜出

“asm-dom”比“virtual-dom”快 2 倍。我怎么也想不到这个结果。WebAssembly 很牛逼。我想比较的也只有 React 的“差异”和“补丁”算法。

asm-dom 结果
[![asm-dom result](../Images/b0cd48f823fcb65d25cefa3730c0771d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NQp540-w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y1xl0j3aljfcvx33rivb.png)

虚拟世界结果
[![virtual-dom result](../Images/649dcd3b5d2eb10bee076a7760e0a3a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---RMu1C8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jsteuw6z6irszihrwzbw.png)

# 用于测量的代码

我把代码上传到了[这个](https://github.com/SatoshiKawabata/asm-dom-vs-virtual-dom)。
你可以像 [hyperscript](https://github.com/hyperhype/hyperscript) 一样定义 dom，这样通过`h()`创建节点，通过`patch()`像虚拟 DOM 一样应用 diff。

asm-dom 规范

```
import init from 'asm-dom';

init().then(asmDom => {
  const { h, patch } = asmDom;

  const root = document.getElementById('root');

  let vnode = h('div', {}, []);
  patch(root, vnode);

  let cnt = 0;
  const id = setInterval(() => {
    console.time('apply patch in 30000 elements');
    const list = [];
    for (let i = 0; i < 30000; i++) {
      list.push(
        h('div', {}, [ Math.random() + '' ])
      );
    }
    const newVnode = h('div', {}, list);
    patch(vnode, newVnode);
    vnode = newVnode;
    console.timeEnd('apply patch in 30000 elements');
    cnt++;
    if (cnt >= 10) {
      clearInterval(id);
    }
  }, 100);
}); 
```

虚拟 dom 代码

```
const h = require('virtual-dom/h');
const diff = require('virtual-dom/diff');
const patch = require('virtual-dom/patch');
const createElement = require('virtual-dom/create-element');

let cnt = 0;
let tree = h('div', {}, []);
let rootNode = createElement(tree);
document.body.appendChild(rootNode);

const id = setInterval(function () {
  console.time('apply patch in 30000 elements');
  const list = [];
  for (let i = 0; i < 30000; i++) {
    list.push(
    h('div', {}, [ Math.random() + '' ])
    );
  }
  const newTree = h('div', {}, list);
  const patches = diff(tree, newTree);
  rootNode = patch(rootNode, patches);
  tree = newTree;
  console.timeEnd('apply patch in 30000 elements');
  cnt++;
  if (cnt >= 10) {
    clearInterval(id);
  }
}, 1000); 
```