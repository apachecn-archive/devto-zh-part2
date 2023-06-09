# 在 Laravel 项目中设置 Jest

> 原文：<https://dev.to/bahdcoder/setting-up-jest-in-a-laravel-project-59b7>

在本教程中，我们将学习如何为客户端测试 laravel 应用程序设置 jest。

### 设置新的 laravel 应用程序

我们将从安装一个新的 laravel 项目开始:

```
laravel new laravel-jest 
```

Enter fullscreen mode Exit fullscreen mode

我们将为这个项目安装所有的 npm 依赖项:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

### 安装和配置 Jest

来装个 jest:

```
 npm install jest --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将为测试框架创建一个 jest 配置文件。在我们的应用程序的根目录下，创建一个`jest.config.js`文件。

```
 // jest.config.js

module.exports = {
  testRegex: 'resources/assets/js/test/.*.spec.js$'
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的`testRegex`配置将 jest 配置为从`resources/assets/js/test`目录中获取测试，并找到任何以`.spec.js`结尾的文件。

### 编写样本测试

在`resources/assets/js/test`目录下，创建一个`index.spec.js`文件，添加一个简单的 jest 测试，比如:

```
 // resources/assets/js/test/index.spec.js

test('it works', () => {
  expect(1 + 1).toBe(2)
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们在我们的`package.json`文件中添加一个测试脚本:

```
 // package.json

"scripts": {
    "test": "jest"
} 
```

Enter fullscreen mode Exit fullscreen mode

要运行我们的示例测试，在您的终端中运行`npm test`,当然我们应该得到一个通过的测试。

### 配置 jest 测试 Vue 组件

为了测试我们的 vue 组件，让我们安装一些我们需要的依赖项:

```
npm i --save-dev vue-jest babel-jest @vue/test-utils 
```

Enter fullscreen mode Exit fullscreen mode

`@vue/test-utils`是 vue 测试库，`babel-jest`，用于配置 jest 使用 babel transpiling，`vue-jest`，用于配置 jest 加载`.vue`文件。

接下来，我们将配置 jest 来使用上面安装的插件:

```
 // jest.config.js

module.exports = {
  testRegex: 'resources/assets/js/test/.*.spec.js$',
  moduleFileExtensions: [
    'js',
    'json',
    'vue'
  ],
  'transform': {
    '^.+\\.js$': '<rootDir>/node_modules/babel-jest',
    '.*\\.(vue)$': '<rootDir>/node_modules/vue-jest'
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的配置中，我们设置 jest 使用`vue-jest` transformer 插件加载任何以`.vue`结尾的文件，使用`babel-jest`插件加载任何以`.js`结尾的文件。

最后，我们需要在 jest 项目的根目录下建立一个`.babelrc`文件，因为`laravel-mix`不公开它的 babel 配置。创建一个`.babelrc`文件，在其中添加如下配置:

```
 // .babelrc

{
  "presets": [
    "env"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试所有这些，我们将为 Laravel 默认附带的`Example.vue`组件编写一个简单的测试:

```
 // resources/assets/js/test/index.spec.js

import { mount } from '@vue/test-utils'
import ExampleComponent from '../components/ExampleComponent.vue'

test('it works', () => {
  expect(1 + 1).toBe(2)
})

test('should mount without crashing', () => {
  const wrapper = mount(ExampleComponent)

  expect(wrapper).toMatchSnapshot()
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在运行我们的测试应该会给你一个成功的结果。