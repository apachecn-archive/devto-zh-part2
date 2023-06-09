# WeeklyRandom #2: lukeed/kleur

> 原文：<https://dev.to/vintagesucks/weeklyrandom-2-lukeedkleur-3184>

*欢迎来到 [**WeeklyRandom**](https://dev.to/t/weeklyrandom) ，这是一个博客系列，在这个系列中，我挑战自己去探索一个随机的 GitHub 库，这个库几乎每周都由我来主演。*

大家看看这次可以探索哪个回购吧。

🥁

```
node index.js vintagesucks
https://github.com/lukeed/kleur 
```

[kleur](https://github.com/lukeed/kleur) by [卢克·爱德华兹](https://github.com/lukeed)。回购的描述如下:

> 用 ANSI 颜色格式化终端文本最快的 Node.js 库~！

很好。让我们试着给我的 Node.js 脚本 [`random-starred-repository`](https://github.com/vintagesucks/random-starred-repository) 添加一些颜色，它支持 WeeklyRandom 的存储库选择。这应该很快完成，但是可以大大提高可读性。

这是我的输出线现在看起来的样子:

```
// index.js
console.log("https://github.com/" + result[randomEntry].owner + "/" + result[randomEntry].repo) 
```

看起来是这样的:

[![random-starred-repository without kleur](img/0eb53fdae477fe64ef916c654510e425.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n1qsVPe4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nikol.as/img/2018/11/kleur-1.png)

我们试试`kleur`。

```
npm install --save kleur 
```

```
// index.js
+ const { green } = require('kleur'); 
-  console.log("https://github.com/" + result[randomEntry].owner + "/" + result[randomEntry].repo)
+  console.log(green().bold(
+    "https://github.com/" + result[randomEntry].owner + "/" + result[randomEntry].repo
+  )) 
```

这是它现在的样子:

[![random-starred-repository with kleur](img/b3bcf64f19a4623648b96dccae0f38bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AMyKqh71--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nikol.as/img/2018/11/kleur-2.png)

🎉

你可以在 GitHub 的[vintage sucks/random-starred-repository](https://github.com/vintagesucks/random-starred-repository)找到完整的代码。非常欢迎有改进的拉动请求！

直到下一次，我将检查我的下一个 [*WeeklyRandom*](https://dev.to/t/weeklyrandom) 库。