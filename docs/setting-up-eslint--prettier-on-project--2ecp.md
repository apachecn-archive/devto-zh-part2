# 在项目上设置 ESLint+beauty🔏

> 原文：<https://dev.to/whoisryosuke/setting-up-eslint--prettier-on-project--2ecp>

随着我创建更多的 React 项目，我发现有很多设置可以让编写代码尽可能的流畅。项目路线图中经常出现的先决条件之一是缺乏林挺设置。

这是一件小事情，通常是风格上的事情，但是你经常会发现自己在这里或那里删除了一个空行，或者在应该由机器人自动完成的地方添加了代码。

所以这里是我在项目中设置 ESLint 和 Prettier 的简单方法(假设你使用 VSCode):

```
# Install ESLint and Babel ESLint
# Make sure to install at least v5.1.0 of ESLint
npm install --save-dev eslint babel-eslint

# Install the Airbnb config
npx install-peerdeps --dev eslint-config-airbnb

# Install Prettier + ESLint config
npm install --save-dev --save-exact prettier eslint-config-prettier 
```

将它添加到项目根目录下的`.eslintrc.js`中:

```
module.exports = {
  parser: "babel-eslint",
  extends: ["airbnb", "prettier"],
  plugins: ["react", "jsx-a11y", "import"]
}; 
```

在 VSCode 中，`CTRL + ,`打开用户设置并添加此项以启用保存时自动修饰:

```
{
    // Format a file on save. A formatter must be available, the file must not be auto-saved, and editor must not be shutting down.
    "editor.formatOnSave": true,
    // Enable/disable default JavaScript formatter (For Prettier)
    "javascript.format.enable": false,
    // Use 'prettier-eslint' instead of 'prettier'. Other settings will only be fallbacks in case they could not be inferred from eslint rules.
    "prettier.eslintIntegration": true
} 
```

> 您还可以为每个项目的设置创建一个名为`.vscode/settings.json`的新文件，以便在所有使用 VSCode 的开发人员中强制执行。

如果你还没有，在 VSCode 中安装 ESLint 并重启你的窗口:

```
code --install-extension dbaeumer.vscode-eslint 
```

> 如果你已经在你的项目中安装了 Prettier(在 NPM 模块中)，你就不需要安装 VSCode 扩展。

就是这样！

保存时，您的代码将自动重新格式化。更重要是，您的代码将针对 linter 运行，并检查任何问题(格式、缺少适当的类型等)。您可以在 VSCode 中的 ESLint 调试器中看到这一点，在 Mac 上使用`CTRL + T`打开终端，然后转到 Problems 选项卡。

希望对你有所帮助

**参考文献**

*   [工作流程——将 ESLint+prettle 添加到项目中](https://gist.github.com/whoisryosuke/1bf81f43a79d2f93f5ba45cb4fe3a0a6)
*   [为 React 开发在 VSCode 中配置 ESLint、Prettier 和 Flow](https://medium.com/@sgroff04/configure-eslint-prettier-and-flow-in-vs-code-for-react-development-c9d95db07213)
*   [Airbnb 斯拉夫配置](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb#eslint-config-airbnb-1)