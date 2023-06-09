# 使用 Webpack 实现 React 应用程序的功能切换

> 原文：<https://dev.to/avraammavridis/implementing-feature-toggles-for-a-react-app-using-webpack-4cpi>

功能切换是一项重要的技术，它可以帮助我们在不同的环境和条件下部署代码，而不会阻止团队中的其他开发人员发布他们的功能。 [Martin Fowler 有一篇关于这个主题的文章](https://martinfowler.com/articles/feature-toggles.html),我不会关注它的理论部分，我想展示一个 react 项目中特性标志的实现。

让我们假设我们正在进行一个 React 项目，其中我们有 4 个环境，

*   发展(地方发展)
*   测试(我们的测试正在运行，又名 NODE_ENV=test)
*   试运行(生产，如环境、缩小捆绑等)
*   生产

让我们假设我们有一个超级实验组件，我们希望在我们的试运行环境中显示它，这样我们可以对它进行 QA，但不能在生产环境中显示。

```
class ExperimentalButton extends Component {

  render() {
    return <Button.Primary {...this.props} />;
  }
} 
```

Webpack 有一个插件可以帮助我们创建功能标志，它叫做[定义插件](https://webpack.js.org/plugins/define-plugin/)

```
new webpack.DefinePlugin({
  'process.env': {
    SHOW_EXPERIMENTAL_BUTTON: true
  }
}); 
```

通常在我们的项目中，我们有`webpack.config.dev.js`和`webpack.config.production.js`，但是没有用于登台的配置，因为我们希望登台和生产的代码是相同的，通常我们在那里部署生产构建。此外，在运行测试之前，我们不会通过 Webpack 传递我们的源代码。那么，我们如何区分阶段/生产/开发，同时避免产生`webpack.config.staging.js`？

在我的例子中，我创建了一个看起来像这样的`featureToggles.json`:

```
{
  "test": {
    "showExperimentalButton": true,
  },
  "development": {
    "showExperimentalButton": true,
  },
  "staging": {
    "showExperimentalButton": true,
  },
  "production": {
    "showExperimentalButton": false,
  }
} 
```

为了区分陈述/生产/开发，我给脚本传递了一个标志

```
 "build:production": "npm run build",
    "build:staging": "npm run build -- --staging",
    "build:development": "npm run build -- --development", 
```

在我的`webpack.config.js`(每个环境的共享配置选项)中，我做到了:

```
const featureSwitches = require('./featureSwitches.json');

let features_env = process.argv[2] === '--staging' ? 'staging' : 'production';
features_env = process.argv[2] === '--development' ? 'development' : features_env;

...
...

new webpack.DefinePlugin({
  'process.env': {
    ...featureSwitches[features_env]
  }
}); 
```

为了显示/隐藏我们的组件，我们将做类似这样的事情

```
{ process.env.showExperimentalButton ? <ExperimentalButton /> : null } 
```

(或者我们可以更进一步，创建一个包装器组件`<FeatureToggle feature='showExperimentalButton'>`。)

但是有一个问题，以前的在测试环境中不工作，因为代码没有通过 Webpack 传递。所以我们无法为组件编写单元测试。首先，我们需要告诉 Jest 在运行测试之前设置一些东西，我们可以使用`setupTestFrameworkScriptFile`选项
并创建一个`jest.init.js`来完成。

```
setupTestFrameworkScriptFile: '<rootDir>/jest.init.js', 
```

在我们的`jest.init.js`文件中，我们将:

```
const featureSwitches = require('./config/featureSwitches');

Object.keys(featureSwitches.test).forEach((key) => {
  process.env[key] = featureSwitches.test[key];
}); 
```

现在我们能够为我们的实验组件运行单元测试了。