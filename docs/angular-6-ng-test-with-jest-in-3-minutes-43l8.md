# angular 6:3 分钟内的笑话“ng 测试”

> 原文：<https://dev.to/jeb/angular-6-ng-test-with-jest-in-3-minutes-43l8>

嘿伙计们。

今天，我将向你展示如何设置你的 Angular CLI 工作区，使之与[Jest](https://jestjs.io/)T2】一起工作，同时保持其清晰的样板代码。

我不会解释为什么你应该选择 Jest 而不是 Karma，假设你已经做了那个选择，但是我会给你一个[链接](https://www.xfive.co/blog/testing-angular-faster-jest/)到一篇解释原因的文章。

所以让我们开始吧！

## 设置笑话

#### 清除业力相关的东西:

```
npm remove karma karma-chrome-launcher karma-coverage-istanbul-reporter karma-jasmine karma-jasmine-html-reporter
rm src/karma.conf.js src/test.ts 
```

#### 安装 [@angular-builders/jest](https://github.com/meltedspark/angular-builders/tree/master/packages/jest) 和`jest`:

```
npm i -D jest @angular-builders/jest 
```

#### 更新您的 Typescript 配置:

尽管你用 Jest 运行你的单元测试，量角器(e2e 测试)仍然需要使用 Jasmine。由于这个事实，您最喜欢的 IDE 可能会与类型混淆，并在单元测试中建议您使用 Jasmine 类型，在 e2e 测试中建议您使用 Jest 类型。为了避免这类问题，您必须明确指定类型:

在*ts config . spec . JSON*(*src*目录或项目根目录，Jest 使用):

```
"compilerOptions": {
 ...
 "module": "commonjs",
 "types": ["jest"]
} 
```

在 *tsconfig.json* (根目录，IDE 使用):

```
"compilerOptions": {
    ...
    "types": ["jest"]
} 
```

#### [中的](#in-angularjson-change-raw-angulardevkitbuildangularkarma-endraw-to-raw-angularbuildersjestrun-endraw-)*angular . JSON*将`@angular-devkit/build-angular:karma`改为`@angular-builders/jest:run`:

```
"projects": {
    ...
    "[your-project]": {
         ...
         "architect": {
                ...
                "test": {
                          "builder": "@angular-builders/jest:run"
                          "options": {
                                ...// see below
                          } 
```

`options`物体的详细描述可以在这里找到[。](https://github.com/meltedspark/angular-builders/tree/master/packages/jest#builder-options)

就是这样，现在你可以用 Jest 运行你的测试了。

## 用 Jest 运行测试

*   为工作区中的所有项目运行测试:`ng test`
*   为一个特定的项目运行测试*我的项目* : `ng test my-project`
*   您可以在构建器选项中指定 [Jest CLI 选项](https://jestjs.io/docs/en/cli.html)(当它是一个持久配置时很有用)或者将 right to `ng test`作为一个参数(当它是一个一次性的时很有用)。例如运行一个单独的测试:`ng test --testNamePattern="My test suite My test case"`

## 进一步阅读

如果你想了解更多关于建筑商的知识，甚至考虑创建一个你自己的建筑商，看看这篇文章。