# 。不推荐使用不带文件扩展名的 eslintrc

> 原文：<https://dev.to/ohbarye/eslintrc-without-file-extension-is-deprecated-3ngg>

## `.eslintrc`文件格式

你可能知道，ESLint 官方鼓励我们给`.eslintrc`一个合适的文件扩展名。

> ESLint 支持多种格式的配置文件:
> 
> *   **JavaScript**——使用`.eslintrc.js`并导出一个包含您的配置的对象。
> *   **YAML**——使用`.eslintrc.yaml`或`.eslintrc.yml`定义配置结构。
> *   **JSON** -使用`.eslintrc.json`定义配置结构。ESLint 的 JSON 文件也允许 JavaScript 风格的注释。
> *   **package . JSON**——在您的`package.json`文件中创建一个`eslintConfig`属性，并在那里定义您的配置。
> *   **已弃用**——使用`.eslintrc`，可以是 JSON，也可以是 YAML。
> 
> [https://eslint . org/docs/user-guide/configuring # configuration-file-formats](https://eslint.org/docs/user-guide/configuring#configuration-file-formats)

这种贬值始于 https://github.com/eslint/eslint/pull/4406 的。是的，差不多两年半前。

## 贡献机会

它被废弃已经有一段时间了。然而，我仍然看到很多 JavaScript 项目使用`.eslintrc`作为 ESLint 的配置文件，甚至在一些大型项目中。

换句话说，有很多机会为 OSS 做贡献。我开始向一些项目发送补丁。

*   [https://github.com/gatsbyjs/gatsby/pull/5206](https://github.com/gatsbyjs/gatsby/pull/5206)
*   [https://github.com/xx45/dayjs/pull/81](https://github.com/xx45/dayjs/pull/81)
*   [https://github . com/wojtekmaj/react-life cycle-methods-diagram/pull/11](https://github.com/wojtekmaj/react-lifecycle-methods-diagram/pull/11)

## 奖金

你觉得它太吹毛求疵了吗？我最初在发送 pull 请求时也有这种感觉，但是下面这句来自 Gatsby.js 维护者的话鼓励我让这个世界变得更好。🙃

> 1000 个吹毛求疵的修复加起来是一个惊人的项目，所以谢谢:-)