# 包裹里是什么？创建 React 应用程序版本

> 原文：<https://dev.to/stereobooster/whats-in-the-bundle-create-react-app-edition-2ob>

如果您想检查基于 Create React App 的项目包中有什么，最简单的方法是使用 [source-map-explorer](https://www.npmjs.com/package/source-map-explorer) :

```
# install it
npm install -g source-map-explorer
# or
yarn global add source-map-explorer
# build the bundle
npm build
# or
yarn build
# inspect the bundle
source-map-explorer build/static/js/main.js 
```

这将为您提供基本信息。如果你觉得这样还不够，你还可以使用 [Webpack 的分析](http://webpack.github.io/analyse/#home)。使用它生成这样的`bundle.json`:

```
NODE_ENV=production ./node_modules/webpack/bin/webpack.js --config node_modules/react-scripts/config/webpack.config.prod.js --profile --json > bundle.json 
```

并配合 [Webpack 的分析](http://webpack.github.io/analyse/#home)使用。摘录自[这张票](https://github.com/facebook/create-react-app/issues/2737)，感谢 jennifersmith。

如果你怀疑你有一个循环依赖，你可以使用[循环依赖检查器](https://www.npmjs.com/package/webpack-cyclic-dependency-checker):

```
# install it
npm install -g webpack-cyclic-dependency-checker
# or
yarn global add webpack-cyclic-dependency-checker
# use bundle.json from the previous step
iscyclic bundle.json 
```