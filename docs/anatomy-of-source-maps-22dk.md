# 源地图剖析

> 原文：<https://dev.to/bugsnag/anatomy-of-source-maps-22dk>

> 这篇博客是关于 JavaScript 调试的两部分系列文章的第一部分。通过代码示例了解 JavaScript 源代码映射的内部工作方式。或者，看看第二部分，了解 JavaScript 错误的[剖析。](https://dev.to/bugsnag/anatomy-of-a-javascript-error-51kc)

源地图是现代 JS 和 CSS 开发的基石。当事情进展顺利时，人们很容易忽略他们扮演的角色有多重要。如果您曾经遇到过源地图出错的情况，它会很快暴露出复杂性，在大多数情况下，它们会掩盖这个复杂性。

你有没有想过它们是用来做什么的，或者它们是如何工作的？源地图的内容是什么样的？并且——如果你打开了一个——`"…GACxB,IAAMC,GAAUD,EAAGE,SAInB…"`映射到底是什么意思？如果是这样，抓紧你的帽子！

## JavaScript 的多种转换

如今，你在编辑器中阅读和编写的代码很少能进入浏览器。转换可以包括:

#### 串联和缩小

串联资产(将多个文件的内容连接成一个文件)减少了提供内容所需的网络请求数量。

缩小的过程，也称为“丑化”，减少了通过网络传输内容所需的字节数。这个过程可能包括将局部变量重命名为更短的标识符、用计算结果替换常量表达式、去除所有空格以及其他复杂的优化。

人们通常使用[丑化](https://github.com/mishoo/UglifyJS2)来缩小。

#### 模块分辨率

将代码分成可管理的代码块是一种技术，这种技术从一开始就为大多数受尊敬的语言所采用。然而，JS 没有提供从其他地方导入代码的方法，所以这个问题通过工具来解决。

[Browserify](http://browserify.org/) 在这方面领先，模仿 Node.js 的[commonjs](http://wiki.commonjs.org/wiki/Modules/1.1.1)ish`require()`功能，最近 [Webpack](https://webpack.js.org/) 到来，它支持节点风格`require()`以及新的 ES 模块`import`规范。

#### 使用新的(或扩展的)JS 特性

随着 JS 规范的发展，使用比受众的浏览器所支持的版本更现代的 JS 已经变得司空见惯。该语言的新特性允许更健壮、简洁和合理的解决方案，因此工程师们热衷于使用它们是可以理解的，但这需要转换。

同样，脸书的 [JSX 扩展](https://reactjs.org/docs/introducing-jsx.html)——为在 React 应用程序中构建类似 DOM 的结构添加语法——是一个广泛使用的功能，但**没有**浏览器支持。

Babel 是将不同“味道”的 JS 转换成更兼容形式的最常用工具。

#### 用完全不同的语言写作

最后，还有一整类编程语言，它们存在的理由就是编译成 JS——[Elm](http://elm-lang.org/)、 [CoffeeScript](http://coffeescript.org/) 、 [TypeScript](https://www.typescriptlang.org/) 、 [PureScript](http://www.purescript.org/) 等等。

## 使用源码图调试 JavaScript 错误

所有这些转换都促进了**编写**代码的更好方式。但是当你运行它的时候，会发生什么事情呢？当你的任务是调查你并不完全熟悉的代码中的错误情况，或者更糟的情况时，会发生什么呢？

**错误示例:**

[![A stacktrace originating from a minified file](../Images/4cf2bf5340766e50f2788856ad83533e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LxhtxzAH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.bugsnag.com/img/posts/source-map-error-min-location.png)

**位置指向这个文件内部的某个地方:**

[![A minified file](../Images/34fde979f683edac888c4a494dea5f58.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x3qVeHDx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.bugsnag.com/img/posts/source-map-minified-file.png)

😩

当面对一堵与编辑器中的任何东西都没有多少相似之处的缩小的代码墙时，逐步通过或找到任何东西的原因的任务可能会非常棘手…

**这里是来源地图的来源**。本质上，源地图允许您回答以下问题:

> 给定这个生成代码中的`line/col`位置，它是在哪个`file`和哪个`line/col`产生的？

## 一个源图里有什么？

源映射格式的第一次迭代是为了在[闭包检查器](https://code.google.com/archive/p/closure-inspector/)中使用而创建的，以帮助调试来自[闭包编译器](https://developers.google.com/closure/compiler/)的混淆的 JS 输出。现在，这种格式正在进行第三次重大修订，由谷歌和 Mozilla 的代表联合撰写。

文件的格式是 JSON。下面是一个来自 [`bugsnag-js`](https://github.com/bugsnag/bugsnag-js) 包的经过裁剪和注释的真实例子:

```
# This is an example source map. Note that source maps are JSON, so comments *aren't* usually allowed!
{
   # "version"
   # Declares which version of the source map spec is being used, like a <!DOCTYPE> in html.
  "version": 3,

  # "sources"
  # A list of input source files that were used to generate the output.
  "sources": [
    "base/lib/es-utils.js",
    "node_modules/stackframe/stackframe.js",
    "base/client.js",
    "base/plugins/throttle.js",
    "browser/plugins/device.js",
    "browser/plugins/inline-script-content.js",
    # … the actual list is a lot longer than this, but the rest is omitted for brevity…
  ],

  # "names"
  # A list of identifiers used in the source code which were changed in or removed from the output.
  "names": [
    "schema", "apiKey", "defaultValue", "message", "validate" #…
  ],

  # "mappings"
  # This is the clever bit! These comma and semi-colon separated values are base64-encoded VLQ
  # values that point from every position in the output back to positions in the input sources.
  "mappings": "CAAA,SAAAA,GAAA,GAAA,iBAAAC,SAAA,…",

  # "sourcesContent"
  # This optional field can include the original source content for each file in
  # the "sources" property. This option should only be omitted if the tool using
  # the source map can retrieve the sources via url or from the filesystem.
  "sourcesContent": [
    "(function(f){if(typeof exports===\"object\"&&typeof…",
    "/*\n * Leaves breadcrumbs when the user interacts…",
    "module.exports = stringify\nstringify.default…",
    "// minimal implementations of useful ES functionality…",
    "const { isoDate } = require('./lib/es-utils')…",
    "const { filter, reduce, keys, isArray } =…",
    "module.exports = client =>\n client.app &&…",
    "(function(root, factory) {\n 'use strict';…",
    "const ErrorStackParser = require('error-stack-parser')…",
    "/**\n * cuid.js\n * Collision-resistant UID generator…",
    "const config = require('./config')\nconst BugsnagReport…",
    "const positiveIntIfDefined = require('../lib/positive-int-check')…",
    "module.exports = {\n releaseStage: {\n…",
    # ……
  ],

  # Some optional fields that are not used in this example…

  # "sourceRoot"
  # A prefix to add to each entry in the "sources" property when looking them up
  # on the network/disk.
  "sourceRoot": "/path/to/static/assets",

  # "file"
  # The name of the file this source map is for.
  "file": "bugsnag.min.js"
} 
```

Enter fullscreen mode Exit fullscreen mode

希望文件的大部分内容都很有意义。显然，如果它是通过网络发送的，就不会有漂亮的空格，也肯定不会有任何注释，但是 JSON 有一些合理命名的属性，不需要太多的麻烦——除了,`"mappings"`键。

简而言之，`"mappings"`是指向`"sources"`和`"names"`数组中条目的指针列表，用于生成的文件的每一行中的每个“段”。每段由逗号`,`分隔，每行由分号`;`分隔。“段”是一个相当模糊的术语，指的是代码行中可以映射回某个原始源的任何部分:标识符、操作符、函数调用等。

可以想象，这些信息会占用很大的空间；在以前版本的源映射中，映射文件最终可能是映射文件大小的 10 倍左右！所以在版本 3 中，引入了一种格式——Base64 VLQs——它为节省空间进行了大量优化。

如果你想了解这种编码背后的完整策略，在 [HTML5 Rocks](https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/#toc-base64vlq) 上有一个关于 Base64 VLQ 格式的精彩解释，但这里我们将集中在几个例子上，这些映射已经被解码，对人类来说模糊不清。

我已经用以下格式的内容替换了`"mappings"`属性:

```
"mappings": {
  "0": [
   ^
   └── the line number of the output file

    "231 => source.js 5:64 foo"
      ^ ^ ^ ^
      │ │ │ └── the symbol name from the source file
      │ │ │
      │ │ └── the line:column position in the source file
      │ │
      │ └── the name of the source file
      │
      └── the column number of the output file

  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

<small>[这是我用来做这个](https://gist.github.com/bengourley/c3c62e41c9b579ecc1d51e9d9eb8b9d2)的代码。</small>

### 检查缩小的源地图(UglifyJS)

首先，我们需要一些代码。这是我写的一个函数，作为维护网球比赛分数的库的一部分:

```
function incrementSet (state, scorer, nonScorer) {
  scorer.games++
  var currentSet = state.players[PLAYER].sets + state.players[OPPONENT].sets
  if (!state.completedSets[currentSet]) state.completedSets[currentSet] = []
  state.completedSets[currentSet][PLAYER] = scorer.isPlayer ? scorer.games : nonScorer.games
  state.completedSets[currentSet][OPPONENT] = scorer.isPlayer ? nonScorer.games : scorer.games
  scorer.games = 0
  nonScorer.games = 0
  scorer.sets = scorer.sets + 1
  state.isFinalSet = scorer.sets + nonScorer.sets === state.config.numSets - 1
  if (scorer.sets > state.config.numSets - scorer.sets) state.isComplete = true
} 
```

Enter fullscreen mode Exit fullscreen mode

当您使用 Uglify 压缩这个函数时，您会得到下面的简化代码和附带的源代码:

```
function incrementSet(e,s,t){s.games++;var m=e.players[PLAYER].sets+e.players[OPPONENT].sets;e.completedSets[m]||(e.completedSets[m]=[]),e.completedSets[m][PLAYER]=s.isPlayer?s.games:t.games,e.completedSets[m][OPPONENT]=s.isPlayer?t.games:s.games,s.games=0,t.games=0,s.sets=s.sets+1,e.isFinalSet=s.sets+t.sets===e.config.numSets-1,s.sets>e.config.numSets-s.sets&&(e.isComplete=!0)}

{
  "version": 3,
  "sources": ["score.js"],
  "names": [
    "incrementSet", "state", "scorer", "nonScorer", "games", "currentSet",
    "players", "PLAYER", "sets", "OPPONENT", "completedSets", "isPlayer",
    "isFinalSet", "config", "numSets", "isComplete"
  ],
  "mappings": {
    "1": [
      "1 => score.js 1:1 incrementSet",
      "10 => score.js 1:10 incrementSet",
      "23 => score.js 1:24 state",
      "25 => score.js 1:31 scorer",
      "27 => score.js 1:39 nonScorer",
      "30 => score.js 2:3 scorer",
      "32 => score.js 2:10 games",
      "40 => score.js 3:3 games",
      "44 => score.js 3:7 currentSet",
      "46 => score.js 3:20 state",
      "48 => score.js 3:26 players",
      "56 => score.js 3:34 PLAYER",
      "64 => score.js 3:42 sets",
      "69 => score.js 3:49 state",
      // SNIP! Truncated for brevity.
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们加载缩小的文件并不带参数调用`incrementSet()`，我们会得到一个类似`TypeError: Cannot read property 'games' of undefined`的错误消息，位置将被报告为第 1 行第 30 列。

参考解码后的映射，我们可以看到第 1 行第 30 列映射到:

```
"30 => score.js 2:3 scorer" 
```

Enter fullscreen mode Exit fullscreen mode

比较输出文件和原始文件:

```
# Output file                                 # Original file

function incrementSet(e,s,t){s.games++;var    scorer.games++
                             ^                ^
 This is line 1, column 30  ─┘                └─ This is line 2, column 3 
```

Enter fullscreen mode Exit fullscreen mode

这完全有道理——我们试图递增`scorer.games`,但是因为我们没有传入任何参数，`scorer`就是`undefined`。

如您所见，来自映射的位置信息还包括它试图修改的对象的原始名称—`scorer`—我们可以看到它被转换为`s`。

### 检查来自编译类型脚本的源地图

坚持以网球为主题的例子，(猜猜这位作者最喜欢的运动是什么？)，这里有一个初出茅庐的打字稿程序:

```
import { Score, Player, nextState } from './utils';

export class Match {
  public score: Score;

  constructor() {
    this.score = new Score();
  }

  public addPoint(p: Player): Score {
    this.score = nextState(this.score, p);
    return this.score;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 编译完成后，您将获得以下 JS 及其附带的源地图:

```
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
var utils_1 = require("./utils");
var Match = (function () {
    function Match() {
        this.score = new utils_1.Score();
    }
    Match.prototype.addPoint = function (p) {
        this.score = utils_1.nextState(this.score, p);
        return this.score;
    };
    return Match;
}());
exports.Match = Match;

{
  "version": 3,
  "file": "index.js",
  "sources": ["index.ts"],
  "names": [],
  "mappings": {
    "1": [""],
    "2": [""],
    "3": [
      "1 => index.ts 1:1", "34 => index.ts 1:52"
    ],
    "4": [
      "1 => index.ts 3:1"
    ],
    "5": [
      "5 => index.ts 6:3"
    ],
    "6": [
      "9 => index.ts 7:5", "13 => index.ts 7:9", "14 => index.ts 7:10",
      "19 => index.ts 7:15", "22 => index.ts 7:18", "26 => index.ts 7:22",
      "39 => index.ts 7:27", "41 => index.ts 7:29", "42 => index.ts 7:30"
    ],
    "7": [
      "5 => index.ts 8:3", "6 => index.ts 8:4"
    ],
    "8": [
      "5 => index.ts 10:10", "29 => index.ts 10:18", "32 => index.ts 10:3",
      "42 => index.ts 10:19", "43 => index.ts 10:28"
    ],
    "9": [
      "9 => index.ts 11:5", "13 => index.ts 11:9", "14 => index.ts 11:10",
      "19 => index.ts 11:15", "22 => index.ts 11:18", "39 => index.ts 11:27",
      "40 => index.ts 11:28", "44 => index.ts 11:32", "45 => index.ts 11:33",
      "50 => index.ts 11:38", "52 => index.ts 11:40", "53 => index.ts 11:41",
      "54 => index.ts 11:42", "55 => index.ts 11:43"
    ],
    "10": [
      "9 => index.ts 12:5", "15 => index.ts 12:11", "16 => index.ts 12:12",
      "20 => index.ts 12:16", "21 => index.ts 12:17", "26 => index.ts 12:22",
      "27 => index.ts 12:23"
    ],
    "11": [
      "5 => index.ts 13:3", "6 => index.ts 13:4"
    ],
    "12": [
      "5 => index.ts 14:1", "17 => index.ts 14:2"
    ],
    "13": [
      "1 => index.ts 14:1", "2 => index.ts 14:2", "2 => index.ts 3:1",
      "6 => index.ts 14:2"
    ],
    "14": [
      "1 => index.ts 3:14", "23 => index.ts 3:19"
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的一点是，映射中的前两行在原始源中没有位置:

```
"use strict";
Object.defineProperty(exports, "__esModule", { value: true }); 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 编译器将这些序言添加到任何模块中，它们与我们编写的任何内容都没有关系，所以没有什么可映射的。

看 JS 输出，最后一行`exports.Match = Match;`是哪里来的？这看起来不像我们在`.ts`文件末尾写的东西……

```
"14": [
  "1 => index.ts 3:14", "23 => index.ts 3:19"
] 
```

Enter fullscreen mode Exit fullscreen mode

所以它来自我们源文件的开头附近…

```
export class Match {
             ^
             └── This is line 3, column 14 
```

Enter fullscreen mode Exit fullscreen mode

我们到了。TypeScript 将静态的`import/export`语句翻译成命令性的节点样式的`require()`调用和`exports`赋值——这是两种完全不同的管理模块的方式，对顺序有不同的约束，这意味着`exports`赋值发生在最后。

这个例子暗示了源地图有多么强大。在缩小示例中，转换后的输出仍然大致类似于输入源，尤其是在顺序方面。在这样一个小例子中，没有源地图的调试似乎很难做到。

然而，在这个 TypeScript 示例中，源映射跟踪了我们在输入开始附近写的东西，它出现在输出结束附近。对于大量的源代码，这样的转换会很快失去控制，拥有一个工具来跟踪它是至关重要的。

### 查看未来 JS 源图

最后，这里有一个使用 ES6/7 及更高版本的特性编写的 JS 程序的示例:

*   默认函数参数
*   班
*   数组析构
*   箭头功能
*   对象静止/展开
*   `const` / `let`声明

为了让这个程序能在大多数浏览器上运行，我们用 [Babel](https://babeljs.io/) 把它编译成 ES5。

```
const createScoreboard = (playerA = 'Player A', playerB = 'Player B') => ({
  names: [playerA, playerB],
  games: [0, 0],
  sets: [0, 0],
  points: [0, 0]
})

const nextScoreboard = (score, scoreboard = createScoreboard()) => {
  // all the tricky logic goes here
}

document.querySelector('button#start', () => {
  const renderer = new MatchRenderer()
  let score = [0, 0]
  let scoreboard = nextScoreboard(score)
  renderer.update(scoreboard)

  // When the button to increment player A's point gets pressed,
  // increment their score and then update the scoreboard
  document.querySelector('button#player-a-point', () => {
    const [a, b] = score
    score = [a + 1, b]
    scoreboard = nextScoreboard(score, scoreboard)
    renderer.render()
  })

  // Do the same for player B
  document.querySelector('button#player-b-point', () => {
    const [a, b] = score
    score = [a, b + 1]
    scoreboard = nextScoreboard(score, scoreboard)
    renderer.render(scoreboard)
  })
})

class MatchRenderer {
  constructor () {
    this.viewData = {
      date: new Date(),
      matchId: Math.random(),
      tournament: 'Bugsnag Masters'
    }
  }
  update (state) {
    updateDOM({ ...this.viewData, ...state })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是编译后的版本:

```
'use strict';

var _extends = Object.assign || function (target) { for (var i = 1; i < arguments.length; i++) { var source = arguments[i]; for (var key in source) { if (Object.prototype.hasOwnProperty.call(source, key)) { target[key] = source[key]; } } } return target; };

var _createClass = function () { function defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } } return function (Constructor, protoProps, staticProps) { if (protoProps) defineProperties(Constructor.prototype, protoProps); if (staticProps) defineProperties(Constructor, staticProps); return Constructor; }; }();

var _slicedToArray = function () { function sliceIterator(arr, i) { var _arr = []; var _n = true; var _d = false; var _e = undefined; try { for (var _i = arr[Symbol.iterator](), _s; !(_n = (_s = _i.next()).done); _n = true) { _arr.push(_s.value); if (i && _arr.length === i) break; } } catch (err) { _d = true; _e = err; } finally { try { if (!_n && _i["return"]) _i["return"](); } finally { if (_d) throw _e; } } return _arr; } return function (arr, i) { if (Array.isArray(arr)) { return arr; } else if (Symbol.iterator in Object(arr)) { return sliceIterator(arr, i); } else { throw new TypeError("Invalid attempt to destructure non-iterable instance"); } }; }();

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

var createScoreboard = function createScoreboard() {
  var playerA = arguments.length > 0 && arguments[0] !== undefined ? arguments[0] : 'Player A';
  var playerB = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 'Player B';
  return {
    names: [playerA, playerB],
    games: [0, 0],
    sets: [0, 0],
    points: [0, 0]
  };
};

var nextScoreboard = function nextScoreboard(score) {
  // all the tricky logic goes here

  var scoreboard = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : createScoreboard();
};

document.querySelector('button#start', function () {
  var renderer = new MatchRenderer();
  var score = [0, 0];
  var scoreboard = nextScoreboard(score);
  renderer.update(scoreboard);

  // When the button to increment player A's point gets pressed,
  // increment their score and then update the scoreboard
  document.querySelector('button#player-a-point', function () {
    var _score = score,
        _score2 = _slicedToArray(_score, 2),
        a = _score2[0],
        b = _score2[1];

    score = [a + 1, b];
    scoreboard = nextScoreboard(score, scoreboard);
    renderer.render();
  });

  // Do the same for player B
  document.querySelector('button#player-b-point', function () {
    var _score3 = score,
        _score4 = _slicedToArray(_score3, 2),
        a = _score4[0],
        b = _score4[1];

    score = [a, b + 1];
    scoreboard = nextScoreboard(score, scoreboard);
    renderer.render(scoreboard);
  });
});

var MatchRenderer = function () {
  function MatchRenderer() {
    _classCallCheck(this, MatchRenderer);

    this.viewData = {
      date: new Date(),
      matchId: Math.random(),
      tournament: 'Bugsnag Masters'
    };
  }

  _createClass(MatchRenderer, [{
    key: 'update',
    value: function update(state) {
      updateDOM(_extends({}, this.viewData, state));
    }
  }]);

  return MatchRenderer;
}(); 
```

Enter fullscreen mode Exit fullscreen mode

及其附带的源图:

```
{  "version":  3,  "sources":  ["tennis.js"],  "names":  [  "createScoreboard",  "playerA",  "playerB",  "names",  "games",  "sets",  "points",  "nextScoreboard",  "score",  "scoreboard",  "document",  "querySelector",  "renderer",  "MatchRenderer",  "update",  "a",  "b",  "render",  "viewData",  "date",  "Date",  "matchId",  "Math",  "random",  "tournament",  "state",  "updateDOM"  ],  "mappings":  {  "1":  [""],  "2":  [""],  "3":  [""],  "4":  [""],  "5":  [""],  "6":  [""],  "7":  [""],  "8":  [""],  "9":  [""],  "10":  [""],  "11":  [  "1 => tennis.js 1:1 createScoreboard",  "5 => tennis.js 1:7 createScoreboard",  "24 => tennis.js 1:26 createScoreboard",  "33 => tennis.js 1:7 createScoreboard",  "49 => tennis.js 1:26 createScoreboard"  ],  "12":  [  "1 => tennis.js 1:26 createScoreboard",  "7 => tennis.js 1:27 playerA",  "14 => tennis.js 1:26 playerA",  "85 => tennis.js 1:37 playerA",  "95 => tennis.js 1:26 playerA"  ],  "13":  [  "1 => tennis.js 1:26 playerA",  "7 => tennis.js 1:49 playerB",  "14 => tennis.js 1:26 playerB",  "85 => tennis.js 1:59 playerB",  "95 => tennis.js 1:26 playerB"  ],  "14":  [  "1 => tennis.js 1:26 playerB",  "10 => tennis.js 1:75 playerB"  ],  "15":  [  "1 => tennis.js 2:3 names",  "12 => tennis.js 2:10 names",  "13 => tennis.js 2:12 playerA",  "20 => tennis.js 2:10 playerA",  "22 => tennis.js 2:21 playerB",  "29 => tennis.js 2:10 playerB",  "30 => tennis.js 1:75 playerB"  ],  "16":  [  "1 => tennis.js 3:3 games",  "12 => tennis.js 3:10 games",  "13 => tennis.js 3:12 games",  "14 => tennis.js 3:10 games",  "16 => tennis.js 3:15 games",  "17 => tennis.js 3:10 games",  "18 => tennis.js 1:75 games"  ],  "17":  [  "1 => tennis.js 4:3 sets",  "11 => tennis.js 4:9 sets",  "12 => tennis.js 4:11 sets",  "13 => tennis.js 4:9 sets",  "15 => tennis.js 4:14 sets",  "16 => tennis.js 4:9 sets",  "17 => tennis.js 1:75 sets"  ],  "18":  [  "1 => tennis.js 5:3 points",  "13 => tennis.js 5:11 points",  "14 => tennis.js 5:13 points",  "15 => tennis.js 5:11 points",  "17 => tennis.js 5:16 points",  "18 => tennis.js 5:11 points"  ],  "19":  [  "1 => tennis.js 1:75 points",  "4 => tennis.js 1:26 points"  ],  "20":  [  "1 => tennis.js 1:26 points",  "2 => tennis.js 1:1 points"  ],  "21":  [""],  "22":  [  "1 => tennis.js 8:1 points",  "5 => tennis.js 8:7 nextScoreboard",  "22 => tennis.js 8:24 nextScoreboard",  "31 => tennis.js 8:7 nextScoreboard",  "45 => tennis.js 8:24 nextScoreboard",  "46 => tennis.js 8:25 score",  "51 => tennis.js 8:24 score",  "53 => tennis.js 8:68 score"  ],  "23":  [  "1 => tennis.js 9:3 score"  ],  "24":  [""],  "25":  [  "1 => tennis.js 8:68 score",  "7 => tennis.js 8:32 scoreboard",  "17 => tennis.js 8:68 scoreboard",  "88 => tennis.js 8:45 createScoreboard",  "106 => tennis.js 8:68 createScoreboard"  ],  "26":  [  "1 => tennis.js 10:2 createScoreboard",  "2 => tennis.js 8:1 createScoreboard"  ],  "27":  [""],  "28":  [  "1 => tennis.js 12:1 document",  "10 => tennis.js 12:10 querySelector",  "23 => tennis.js 12:1 querySelector",  "24 => tennis.js 12:24 querySelector",  "38 => tennis.js 12:1 querySelector",  "40 => tennis.js 12:40 querySelector",  "52 => tennis.js 12:46 querySelector"  ],  "29":  [  "1 => tennis.js 13:3 querySelector",  "7 => tennis.js 13:9 renderer",  "18 => tennis.js 13:20 renderer",  "22 => tennis.js 13:24 MatchRenderer",  "35 => tennis.js 13:20 MatchRenderer",  "37 => tennis.js 13:3 MatchRenderer"  ],  "30":  [  "1 => tennis.js 14:3 MatchRenderer",  "7 => tennis.js 14:7 score",  "15 => tennis.js 14:15 score",  "16 => tennis.js 14:17 score",  "17 => tennis.js 14:15 score",  "19 => tennis.js 14:20 score",  "20 => tennis.js 14:15 score",  "21 => tennis.js 14:3 score"  ],  "31":  [  "1 => tennis.js 15:3 score",  "7 => tennis.js 15:7 scoreboard",  "20 => tennis.js 15:20 nextScoreboard",  "35 => tennis.js 15:35 score",  "40 => tennis.js 15:20 score",  "41 => tennis.js 15:3 score"  ],  "32":  [  "1 => tennis.js 16:3 renderer",  "12 => tennis.js 16:12 update",  "18 => tennis.js 16:3 update",  "19 => tennis.js 16:19 scoreboard",  "29 => tennis.js 16:3 scoreboard"  ],  "33":  [""],  "34":  [  "1 => tennis.js 18:3 scoreboard"  ],  "35":  [  "1 => tennis.js 19:3 scoreboard"  ],  "36":  [  "1 => tennis.js 20:3 document",  "12 => tennis.js 20:12 querySelector",  "25 => tennis.js 20:3 querySelector",  "26 => tennis.js 20:26 querySelector",  "49 => tennis.js 20:3 querySelector",  "51 => tennis.js 20:51 querySelector",  "63 => tennis.js 20:57 querySelector"  ],  "37":  [  "1 => tennis.js 20:57 querySelector",  "18 => tennis.js 21:22 score",  "23 => tennis.js 20:57 score"  ],  "38":  [  "1 => tennis.js 20:57 score"  ],  "39":  [  "1 => tennis.js 20:57 score",  "9 => tennis.js 21:13 a",  "10 => tennis.js 20:57 a"  ],  "40":  [  "1 => tennis.js 20:57 a",  "9 => tennis.js 21:16 b",  "10 => tennis.js 20:57 b"  ],  "41":  [""],  "42":  [  "1 => tennis.js 22:5 score",  "13 => tennis.js 22:13 score",  "14 => tennis.js 22:15 a",  "18 => tennis.js 22:19 a",  "19 => tennis.js 22:13 a",  "21 => tennis.js 22:22 b",  "22 => tennis.js 22:13 b",  "23 => tennis.js 22:5 b"  ],  "43":  [  "1 => tennis.js 23:5 scoreboard",  "18 => tennis.js 23:18 nextScoreboard",  "33 => tennis.js 23:33 score",  "38 => tennis.js 23:18 score",  "40 => tennis.js 23:40 scoreboard",  "50 => tennis.js 23:18 scoreboard",  "51 => tennis.js 23:5 scoreboard"  ],  "44":  [  "1 => tennis.js 24:5 renderer",  "14 => tennis.js 24:14 render",  "20 => tennis.js 24:5 render"  ],  "45":  [  "1 => tennis.js 25:4 render",  "4 => tennis.js 20:3 render"  ],  "46":  [""],  "47":  [  "1 => tennis.js 27:3 render"  ],  "48":  [  "1 => tennis.js 28:3 document",  "12 => tennis.js 28:12 querySelector",  "25 => tennis.js 28:3 querySelector",  "26 => tennis.js 28:26 querySelector",  "49 => tennis.js 28:3 querySelector",  "51 => tennis.js 28:51 querySelector",  "63 => tennis.js 28:57 querySelector"  ],  "49":  [  "1 => tennis.js 28:57 querySelector",  "19 => tennis.js 29:22 score",  "24 => tennis.js 28:57 score"  ],  "50":  [  "1 => tennis.js 28:57 score"  ],  "51":  [  "1 => tennis.js 28:57 score",  "9 => tennis.js 29:13 a",  "10 => tennis.js 28:57 a"  ],  "52":  [  "1 => tennis.js 28:57 a",  "9 => tennis.js 29:16 b",  "10 => tennis.js 28:57 b"  ],  "53":  [""],  "54":  [  "1 => tennis.js 30:5 score",  "13 => tennis.js 30:13 score",  "14 => tennis.js 30:15 a",  "15 => tennis.js 30:13 a",  "17 => tennis.js 30:18 b",  "21 => tennis.js 30:22 b",  "22 => tennis.js 30:13 b",  "23 => tennis.js 30:5 b"  ],  "55":  [  "1 => tennis.js 31:5 scoreboard",  "18 => tennis.js 31:18 nextScoreboard",  "33 => tennis.js 31:33 score",  "38 => tennis.js 31:18 score",  "40 => tennis.js 31:40 scoreboard",  "50 => tennis.js 31:18 scoreboard",  "51 => tennis.js 31:5 scoreboard"  ],  "56":  [  "1 => tennis.js 32:5 renderer",  "14 => tennis.js 32:14 render",  "20 => tennis.js 32:5 render",  "21 => tennis.js 32:21 scoreboard",  "31 => tennis.js 32:5 scoreboard"  ],  "57":  [  "1 => tennis.js 33:4 scoreboard",  "4 => tennis.js 28:3 scoreboard"  ],  "58":  [  "1 => tennis.js 34:2 scoreboard",  "2 => tennis.js 12:1 scoreboard"  ],  "59":  [""],  "60":  [  "5 => tennis.js 36:7 MatchRenderer",  "18 => tennis.js 36:7 MatchRenderer"  ],  "61":  [  "1 => tennis.js 37:3 MatchRenderer",  "28 => tennis.js 37:18 MatchRenderer"  ],  "62":  [  "1 => tennis.js 37:18 MatchRenderer"  ],  "63":  [  ""  ],  "64":  [  "1 => tennis.js 38:5 MatchRenderer",  "10 => tennis.js 38:10 viewData",  "18 => tennis.js 38:5 viewData",  "21 => tennis.js 38:21 viewData"  ],  "65":  [  "1 => tennis.js 39:7 date",  "13 => tennis.js 39:13 date",  "17 => tennis.js 39:17 Date",  "21 => tennis.js 39:13 Date",  "23 => tennis.js 38:21 Date"  ],  "66":  [  "1 => tennis.js 40:7 matchId",  "16 => tennis.js 40:16 Math",  "21 => tennis.js 40:21 random",  "27 => tennis.js 40:16 random",  "29 => tennis.js 38:21 random"  ],  "67":  [  "1 => tennis.js 41:7 tournament",  "19 => tennis.js 41:19 tournament"  ],  "68":  [  "1 => tennis.js 38:21 tournament",  "6 => tennis.js 38:5 tournament"  ],  "69":  [  "1 => tennis.js 43:4 tournament"  ],  "70":  [""],  "71":  [""],  "72":  [""],  "73":  [  "28 => tennis.js 44:11 state",  "33 => tennis.js 44:11 state",  "35 => tennis.js 44:18 state"  ],  "74":  [  "1 => tennis.js 45:5 updateDOM",  "30 => tennis.js 45:20 updateDOM",  "35 => tennis.js 45:25 viewData",  "43 => tennis.js 45:5 viewData",  "45 => tennis.js 45:38 state",  "50 => tennis.js 45:5 state"  ],  "75":  [  "1 => tennis.js 46:4 state"  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们深入到有趣的部分。

#### 默认参数

我们可以在第 12 行和第 13 行看到一些代码，看起来不像源代码中的任何东西:

```
var playerA = arguments.length > 0 && arguments[0] !== undefined ? arguments[0] : 'Player A';
var playerB = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 'Player B'; 
```

Enter fullscreen mode Exit fullscreen mode

通过在映射中查找，我们可以看到它起源于何处:

```
"12":  [  "1 => tennis.js 1:26 createScoreboard",  "7 => tennis.js 1:27 playerA",  "14 => tennis.js 1:26 playerA",  "85 => tennis.js 1:37 playerA",  "95 => tennis.js 1:26 playerA"  ],  "13":  [  "1 => tennis.js 1:26 playerA",  "7 => tennis.js 1:49 playerB",  "14 => tennis.js 1:26 playerB",  "85 => tennis.js 1:59 playerB",  "95 => tennis.js 1:26 playerB"  ], 
```

Enter fullscreen mode Exit fullscreen mode

输出中的这两行都源自我们原始源代码的第 1 行:

```
const createScoreboard = (playerA = 'Player A', playerB = 'Player B') => ({
                         ^^         ^           ^         ^
      columns:        26 ┘└ 27  37 ─┘       49 ─┘     59 ─┘ 
```

Enter fullscreen mode Exit fullscreen mode

从这些映射中可以清楚地看出，默认的函数参数是如何被转换成在不支持该特性的环境中工作的。

#### 数组析构，剩余/扩散，类

与 TypeScript 示例一样，第 1-10 行是由 Babel 插入的。同样，它们是在转换过程中创建的，但与输入源的部分没有直接关系，因此它们不会映射到任何地方。当我们看看当我们使用数组析构、对象扩展操作符和类语法时发生了什么，我们很快就会明白为什么:

```
const [ a, b ] = score           =>   var _score = score,
                                          _score2 = _slicedToArray(_score, 2),
                                          a = _score2[0],
                                          b = _score2[1];

{ ...this.viewData, ...state }   =>   _extends({}, this.viewData, state);

class MatchRenderer {            =>   _createClass(MatchRenderer, [{ 
```

Enter fullscreen mode Exit fullscreen mode

巴贝尔在这里做的是一个权衡。按照默认参数的例子，*将有可能在每次交换一个特性时映射每个转换的直接结果。然而，由于这将输出一个更大的文件，它创建了您在输出的第 1-10 行看到的助手函数。可以映射这些函数的用法；例如，下面是`_extends(…)`调用的映射:* 

```
"74": [
  "1 => tennis.js 45:5 updateDOM", "30 => tennis.js 45:20 updateDOM",
  "35 => tennis.js 45:25 viewData", "43 => tennis.js 45:5 viewData",
  "45 => tennis.js 45:38 state", "50 => tennis.js 45:5 state"
]

    updateDOM({ ...this.viewData, ...state })
    ^              ^    ^            ^
  5 ┘          20 ─┘    └─ 25        └─ 38 
```

Enter fullscreen mode Exit fullscreen mode

由于源代码中的许多地方最终会调用`_extends()`、`_slicedToArray()`或`_createClass()`助手中的代码，所以不可能有明确的映射。这是该过程的一个限制。

## 来源图+ Bugsnag

在 Bugsnag，我们非常关心以最有用和最可行的方式显示错误报告。如果你发布的是转换后的 JS，那么对照生成的代码查看你的 stacktraces 有什么用呢？这就是我们使用源代码映射的原因——根据您在编辑器中看到的代码显示堆栈跟踪。

关于源地图格式的好消息是，它在很大程度上是语言不可知的。我们在这里主要讨论了作为目标语言的 JS，但是它也常用于生成 CSS。这意味着，如果你选择一些新的 JS 的深奥味道，或者甚至一些我们从未听说过的编译成 JS 的语言，我们已经得到你了！使用源图，我们可以向您显示在您生成的 JS 中发生的错误的原始来源。

<small>*好吧，你可能看不到完美的语法高亮！</small>

### 如何告诉 Bugsnag 关于你的源地图

当您想让源地图与 Bugsnag 一起工作时，有两种主要方法可以采用:

#### 自托管

最简单的方法是将源地图与捆绑的 JS 放在一起，然后:

*   在捆绑的 JS 中包含`//# sourceMappingURL=`注释。这是由大多数生成源地图的工具自动插入的。一旦我们加载了 JS，我们就会检测到这一点，如果地图存在的话，我们就去获取它。
*   提供捆绑的 JS 时，设置`X-SourceMap`头。当我们收到一个源自你的 JS 的错误时，我们会请求加载它。我们将检测`X-SourceMap`头，如果它存在，我们将获取地图。

我们将总是从同一个 IP 请求源和地图，因此使用这些选项中的任何一个，如果公开您的源地图不是一个选项，您可以[将我们的 IP](https://docs.bugsnag.com/platforms/browsers/source-maps/#access-through-firewall)列入白名单。

#### 上传

您也可以选择将您的源地图直接上传给我们。根据您使用的工具，您可以在不同的级别上执行此操作:

*   [webpack-bugsnag-plugins](https://docs.bugsnag.com/build-integrations/webpack/#uploading-source-maps) 允许你通过我们的`BugsnagSourceMapUploaderPlugin`插件直接从 webpack 上传你的源地图。
*   bugsnag-sourcemaps 允许你从一个节点进程中或者通过命令行上传你的源地图。
*   或者你可以直接点击我们的[源地图 API](https://docs.bugsnag.com/api/js-source-map-upload/#curl-example) 。

## 鳍。

如果你做到了这一步，你就是名副其实的地图兵。希望你对这个不起眼的源地图有了新的认识，至少，你可以用你关于`"mappings"`的知识给你的朋友留下深刻印象。快乐的代码转换！

* * *

试试 Bugsnag 的 [JavaScript 错误报告](https://www.bugsnag.com/platforms/javascript/)或者在我们的[文档](https://docs.bugsnag.com/platforms/browsers/)中了解更多。