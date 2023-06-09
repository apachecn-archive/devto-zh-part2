# 什么时候改变一个项目的版本号是正确的？

> 原文：<https://dev.to/ghost/when-is-it-right-to-change-the-version-number-of-a-project--145c>

为了理智起见:

```
let versionstring = '1.2.3';
let masterversion = 'the first digit of the version string (e.g. 1)';
let version = 'the second digit of the version string (e.g. 2)';
let subversion = 'the third digit of the version string (e.g. 3)'; 
```

Enter fullscreen mode Exit fullscreen mode

作为一个最近才写了一个实际上有用的代码库的人(而且是有效的！)，我还没有理解关于版本、贡献和许可的 50 万件事情，但是在过去的一周里我遇到的最紧迫的问题是:

*   什么时候是一个 web 应用程序升级的合适时机？

当你修复了一个 bug 后，你会升级 subversion 吗？如果你做了一个巨大的改变，将颠覆提升一个等级可以吗？什么时候是更换版本或主版本的合适时机？

你为你的项目做什么？还有你多久换一次 master version/version/subversion？你会为每个版本发布变更日志吗，或者只是一些大的改动？

如果你发布变更日志，如果它是一个私有库，你怎么做？