# MobX 提示:新的 Api 命名为 flow(令人困惑的名称...)

> 原文：<https://dev.to/acro5piano/mobx-tips-new-api-named-flow-confusing-name-3gjk>

# 3.x

在 MobX 3.x 中，编写异步动作有点冗长。

```
import { observable, runInAction } from 'mobx'
import { someApi } from '../api'

class SomeStore {
    @observable someState = ''

    async someAction() {
        const res = await someApi.fetch()
        runInAction(() => {
            this.someState = res.data
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 4.x

MobX 4.x 有一个新的 API 叫`flow`，真的很混乱。每次我谷歌`mobx flow`，脸书的静态打字工具就会出现。

```
import { observable, flow } from 'mobx'
import { someApi } from '../api'

class SomeStore {
    @observable someState = ''

    someAction = flow(function * () {
        this.someState = yield someApi.fetch()
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

我不知道上面发生了什么，但是代码变得更短更酷了，有了生成器函数和类属性。

如果我们在严格模式下使用 using MobX 的 flow + TypeScript，尝试将这个绑定到生成器函数中的类，就像这样:

```
 public someAction = flow(function * (this: SomeStore) {
        this.someState = yield someApi.fetch()
    }) 
```

Enter fullscreen mode Exit fullscreen mode

(我花了一个小时找到这个解决方案)

# 许诺

如果我们考虑错误处理，Promise 变得更简单:

```
import { observable, action } from 'mobx'
import { someApi } from '../api'

class SomeStore {
    @observable someState = ''
    @observable error = null

    someAction() {
        someApi.fetch().then(
            action(res => this.someState = res.data),
            action(err => this.err = err),
        )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode