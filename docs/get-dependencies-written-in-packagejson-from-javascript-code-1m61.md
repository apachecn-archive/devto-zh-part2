# 从 JavaScript 代码中获取 package.json 中编写的依赖项

> 原文：<https://dev.to/ohbarye/get-dependencies-written-in-packagejson-from-javascript-code-1m61>

这只是一个关于如何从 JavaScript 代码引用用`package.json`写的依赖关系的备忘录。

```
import pkg from './package.json';

const dependencies = Object.keys(pkg.dependencies);
const devDependencies = Object.keys(pkg.devDependencies);
const peerDependencies = Object.keys(pkg.peerDependencies); 
```

Enter fullscreen mode Exit fullscreen mode

这在`rollup.config.js`中指定`externals`的情况下会很有用。

```
{
  external: Object.keys(pkg.dependencies)
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode