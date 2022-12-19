# 一个业余爱好者的冒险~第五部分

> 原文：<https://dev.to/link2twenty/adventures-of-a-hobbyist--part-five-532c>

# 多思考一点`conf`文件

[![bash](../Images/5034ef7c0adf8a4c49a985b08eb1c2c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3A7NSV9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.pixabay.com/photo/2013/07/13/13/41/bash-161382_960_720.png)

## 这是什么？

这是一个正在进行的系列的第 5 部分，在这个系列中，我了解了 NodeJS，最终目标是开发一些开源软件，让我的同事和世界上其他 IT 支持团队的生活变得更加轻松。

正如我提到的，这是第五部分，以下是前四部分:

*   [序言](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-one-2e0n)
*   [学习第一周](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-two-2g5a)
*   [思考`conf`文件](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-three-l1m)
*   [玩弄`MySQL`和`HTTP`T3】](https://dev.to/link2twenty/adventures-of-a-hobbyist--part-four-2i9k)

## 我们不是已经这样做了吗？

我们确实已经看过了`.conf`的文件，但从那以后我得到了一些建议，走得更远了，我想，我已经决定了我要走的大致路线。

### 当前`conf`文件

我在想，我可能会有一个这样的文件来存储某些我不想放在数据库中的信息。我认为这里有一个插件列表可能是一个好主意，我正在制作的程序将主要是插件。

```
{  "General":  {  "name":  "ignis"  },  "Plugin_list":  {}  } 
```

Enter fullscreen mode Exit fullscreen mode

## 你有什么解决办法？

我发现了一个名为 [conf](https://www.npmjs.com/package/conf) 的模块，它让你输入一个文件位置，它希望格式是 JSON，然后有一堆方法与数据交互。

我也喜欢使用事件监听器监听键变化的想法，但是不幸的是，conf 没有提供这个功能，所以我决定写一个可以导入的包装器。

### 代码

这是今天的代码，虽然我现在对它做了很多调整。

```
const Conf = require('conf');
const EM = require('events');

/** Class helper for reading and writing to the conf file. */
class ConfHelper {
  /**
   * Initalise file.
   * @param {string} cn - config name, without extension. Default: 'ignis'
   * @param {string} fe - file extension. Default: 'conf'
   * @param {string} loc - file location, from current location. Default '.'
   */
  constructor(cn='ignis',fe='conf',loc='.') {
    this.events = new EM.EventEmitter();
    this.config = new Conf({configName: cn, fileExtension: fe, cwd: loc});
    this.conf   = this.config.get();
  }
  /** 
   * Update `this.conf` and trigger event. 
   * @event change
   * @private
   */
  _getConf(key = '', type) {
    this.conf = this.config.get();
    this.events.emit('change', key, type);
  }
  /** Create new key. */
  createKey(key = '', val) {
    if (this.config.has(key)) throw `${key} already exists, please use updateConf`
    let keyVal = this.conf;
    let layers = key.split('.');
    let name = layers[layers.length - 1];
    for (let i = 0; i < layers.length - 1; i++) {
      if (!keyVal[layers[i]]) keyVal[layers[i]] = {};
      keyVal = keyVal[layers[i]];
    }
    keyVal[name] = val;
    this.config.set(layers[0], this.conf[layers[0]]);
    this._getConf(key, "create");
  }
  /** Delete existing key. */
  deleteKey(key = '') {
    if (!this.config.has(key)) return
    this.config.delete(key);
    this._getConf(key, "delete");
  }
  /** Update existing key. */
  updateKey(key = '', val) {
    if (!this.config.has(key)) throw `${key} does not exists please use createConf`
    if (this.config.get(key) === val) return
    this.config.set(key, val);
    this._getConf(key, "update");
  }
}

module.exports = ConfHelper; 
```

Enter fullscreen mode Exit fullscreen mode

这是我第一次导出模块，所以我不知道我是否遵循了最佳实践，一如既往，欢迎在评论中给我一些纠正。

如你所见，我有一个名为`_getConf`的方法，它发出一个变更事件，带有被变更的键和变更类型，同样不确定这是否是最佳实践，但它似乎对我有用。

### 简单测试

我做了一个简单的测试来测试事件系统，并确保它能够读写`conf`文件。

```
const ConfHelper = require('./conf_import');
const ch = new ConfHelper()

ch.events.on('change', (key, type) => {
  let event =`
  -------
  type ${type} key ${key} newVal ${ch.config.get(key)} -------`;
  console.log(event)
});

ch.createKey('General.version', "v0.0.1");
ch.updateKey('General.version', "v0.0.2");
ch.deleteKey('General.version'); 
```

Enter fullscreen mode Exit fullscreen mode

我有一个事件侦听器，它打印出一个小表，显示更改的类型、更改的键，然后读取数据以获得新值。然后我创建一个新的键，更新那个键，然后删除它。

下面是输出结果

```
-------
type    create
key     General.version
newVal  v0.0.1
-------

-------
type    update
key     General.version
newVal  v0.0.2
-------

-------
type    delete
key     General.version
newVal  undefined
------- 
```

Enter fullscreen mode Exit fullscreen mode

## 现在怎么办？

好了，现在我想听一些意见，这是一个体面的做事方式吗？我想创建一个方法来读取文件，并检查自上次读取以来的更改，然后报告它们，但这将是下周的问题。

### 概述

我会预见插件的安装/更新脚本看起来有点像这样。

```
const ConfHelper = require('./conf_import');
const ch = new ConfHelper();

const key = 'SambaAD'
const ver = 'v0.4.3'

if (!ch.conf.Plugin_list[key]) {
  install();
} else if (ch.conf.Plugin_list[key] !== ver) { // this is actually a mismatch so we're not sure if it's an upgrade or a roll back.
  update();
} else {
  console.log(`${key} is already on the latest version (${ver})`);
  // uninstall included for test purposes
  uninstall()
}

function install() {
  // some install stuff
  ch.createKey(`Plugin_list.${key}`, ver);
  ch.createKey(key, {});
  ch.createKey(`${key}.name`, 'Samba AD');
  ch.createKey(`${key}.description`, 'Controller for Samba AD');
  ch.createKey(`${key}.author`, 'Andrew Bone');
  ch.createKey(`${key}.help`, 'https://lmgtfy.com');
}

function update() {
  // some update stuff
  ch.updateKey(`Plugin_list.${key}`, ver);
}

function uninstall() {
  // some delete stuff
  ch.deleteKey(key);
  ch.deleteKey(`Plugin_list.${key}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

照现在的样子运行它会导致`ignis.conf`被更新如下

```
{  "General":  {  "name":  "ignis"  },  "Plugin_list":  {  "SambaAD":  "v0.4.3"  },  "SambaAD":  {  "name":  "Samba AD",  "description":  "Controller for Samba AD",  "author":  "Andrew Bone",  "help":  "https://lmgtfy.com"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## `</post>`

这就是我们的另一篇文章，谢谢你的阅读。我写这些帖子的目的是让我的旅程变得有趣，吸引人，对读者和我都有好处。如果有什么我能做的让我的帖子更好，请告诉我。我在过去收到的评论都很棒，发帖后与社区交流无疑是一个亮点。

再次感谢🦄