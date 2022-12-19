# 用 jest runner 连续运行流类型检查

> 原文：<https://dev.to/binygal/continuously-running-flowtype-checks-with-jest-runner-af9>

几周前，我成为了一个新项目的一部分。我做的第一件事是建立我的常规工作空间，为编码做准备。我通常的设置是在一个屏幕上显示 VSCode，iTerm 在另一个屏幕上运行 Jest，Chrome 在后台打开。一切都很完美，我拿了一杯咖啡，坐在我的桌子前，意识到缺少了一些东西…这个项目是用 Flowtype 配置的，我希望它在我的屏幕上连续运行，如果我错过了一些类型定义，它会向我显示(我是一个健忘的程序员，在向我的堆栈添加新技术时，我需要不断的提醒)。

我的问题是 Flow 没有观察模式。

#### 什么是 Jest runner？

Jest 是脸书的测试平台。随着 Jest 变得越来越成熟，其中实现了许多功能，核心团队明白运行测试只是 Jest 功能的一小部分。Jest 的一个能力是监视模式，可以在文件改变时重新运行。

从 jest 的第 21 版开始，可以用你自己的运行器替换 Jest 测试运行器，这带来了很多机会，比如使用 Jest 平台运行 Mocha 测试，运行 eslint 林挺，以及在我们的例子中运行 Flowtype 检查。

(如果你想了解 Jest 作为一个平台的更多信息，你应该看看下面的惊人之谈)

[https://www.youtube.com/embed/NtjyeojAOBs](https://www.youtube.com/embed/NtjyeojAOBs)

根据 [Jest 文档](https://facebook.github.io/jest/docs/en/configuration.html#runner-string)runner 应该返回一个带有 runTests 函数的对象。当 Jest 运行我们的 runner 时，它会传递给它一些回调函数，其中一个是 onResult，我们应该调用这个函数，测试结果要么失败，要么通过。

下面是 runTests 函数的具体实现:

我们将从存储开始时间开始，并立即使用 child_process 执行流检查(注意，runTests 应该返回一个承诺来通知 jest 测试已经完成，因此所有内容都封装在承诺中)。

```
const start = +new Date();
exec('flow', { stdio: 'ignore', cwd: process.cwd() }, (err, stdout) => { 
```

接下来，我们将使用作为 exec 回调中的 stdout 参数接收的输出来生成一个包含所有错误的对象。对象的结构以文件路径作为属性名，该路径的错误值如下:

`index.js: error: String should be int`

```
const errors = stdout.split('Error');
const errorsPerFile = errors.reduce((previous, current) => {           
  const firstErrorLine = current.split('\n')[0]; 
  const fileNameMatcher = firstErrorLine.match(/( **\.** {1,2}| **\/** )?([A-z]| **\/** |-)\ ***\.** js(x?)/);          
  if (fileNameMatcher) {            
    const fileName = path.join(process.cwd(), fileNameMatcher[0]);            
    const errorMessage = current.substring(current.indexOf('\n') + 1);            
    if (!previous[fileName]) {
      previous[fileName] = [];            
    }            
    previous[fileName].push(errorMessage);
  }          
  return previous;
}, {}); 
```

现在我们有了每个文件的错误对象，我们只需要迭代我们所有的测试，看看它们是否在那个对象中有一个条目。如果它们有条目，则意味着我们在该文件中有错误，否则我们可以将该文件标记为通过。我们将使用来自 [create-jest-runner](https://github.com/rogeliog/create-jest-runner) 的 pass 和 fail 函数来标记测试的状态。在方法的最后，我们将解析我们返回的承诺。

```
tests.forEach((t) => {        
  let testResults;        
  if (errorsPerFile[t.path]) {        
    testResults = fail({      
      start,              
      end: +new Date(),        
      test: { path: t.path, errorMessage: errorsPerFile[t.path] },            
    });        
  } else {        
    testResults = pass(
      { start, end: +new Date(), test: { path: t.path } });          
  }          
  onResult(t, testResults);        
  });        
resolve(); 
```

就是这样。我很想听听你对这个帖子的想法，以及你认为还可以用 Jest-Runners 做些什么。请在 Twitter 上留下评论或给我发 DM。完整的代码可以在 [repo](https://github.com/binygal/jest-runner-flowtype) 中找到。