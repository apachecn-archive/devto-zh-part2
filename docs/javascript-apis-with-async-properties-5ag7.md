# 具有异步属性的 Javascript APIs

> 原文：<https://dev.to/omrilotan/javascript-apis-with-async-properties-5ag7>

Getter 属性是屏蔽逻辑和公开静态值的好方法。我发现区分功能和属性非常优雅。这个逻辑可能隐藏仅按需执行的惰性计算，或者它可能隐藏基于对象甚至应用程序状态的逻辑。

例如，用户对象可能具有类似 user.goOnline()的功能，它应该具有类似 user.isOnline 的属性。检查用户是否在线应该按需执行，因为这种状态可能从实例化时刻到查询时刻发生变化。

随着 Javascript 中 async/await 的出现，现在可以通过将它们指向承诺来创建这种懒惰的 getters，从而创建更多的语义 API。

```
class User {
    constructor(id) { ... }
        goOnline() { ... }
        addBadge(type) { ... }
        get _isOnline() {  
        return fetch(`/${this.username}/online`)
            .then(response => response.json())  
            .then(data => data.isOnline)  
            .catch(error => { throw error; });  
    } 
}

const myUser = new User ('acb33259');

// Functionality
myUser.goOnline();

// Attribute
if (await myUser.isOnline) {

    // Functionality
    myUser.addBadge('online');
} 
```

Enter fullscreen mode Exit fullscreen mode

静态对象也可以利用这种类型。指向异步属性的 API 对象看起来也更具可读性。

```
const api = {
    get request() {
        return new Promise (resolve => setTimeout(() => resolve({status: 200}), 1000))
    }
};

(async () => {
    const { status } = await api.request;
    console.log(status); // 200
})(); 
```

Enter fullscreen mode Exit fullscreen mode

当在模块之间导入时——我觉得吸引力更大。

```
module.exports = async () => {
    const { request } = require('./api');
    const { data } = await request;

    // Do amazing things with the data
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就打开了无尽的甜言蜜语的句法可能性。

下面是一个(简化的)真实例子，我发现使用异步 getters 可以使最终的逻辑更加清晰。

`git tag -a ${tag} -m "${await message}"`

```
const asyncExec = require('util').promisify(require('child_process').exec);

/**
 * Execute command line in a child process
 * @param  {...string} args Commands
 * @return {string}
 */
async function exec (...args) {
  const { stdout, stderr } = await asyncExec(...args);

  if (stderr) {
    throw new Error(stderr);
  }

  return stdout.trim();
}

/**
 * @typedef           gitData
 * @description       Git data getters
 * @type     {Object}
 * @property {string} author  Author of the last commit
 * @property {string} email   Git user email
 * @property {string} message Most recent commit message
 */
const gitData = Object.defineProperties({}, {
  author:  { get: async () => await exec('git log -1 --pretty=%an') },
  email:   { get: async () => await exec('git log -1 --pretty=%ae') },
  message: { get: async () => await exec('git log -1 --pretty=%B') },
});

/**
 * Create a tag by the last commit's author with it's message
 * @param  {string} tag Tag name (e.g. v1.1.0)
 * @return {void}
 */
module.exports = async (tag) => {
  const { message, author, email } = gitData;

  try {
    await exec(`git config --global user.name "${await author}"`);
    await exec(`git config --global user.email "${await email}"`);
    await exec(`git tag -a ${tag} -m "${await message}"`);
    await exec(`git push origin refs/tags/${tag}`);
  } catch (error) {
    console.error(error);
    throw error;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode