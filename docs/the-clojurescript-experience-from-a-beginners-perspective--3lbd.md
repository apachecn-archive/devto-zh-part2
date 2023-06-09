# 初学者视角下的 ClojureScript 体验

> 原文：<https://dev.to/kleinrein/the-clojurescript-experience-from-a-beginners-perspective--3lbd>

对 ClojureScript 生态系统的快速了解，以及对 ClojureScript 感兴趣的开发者的入门指南。

## 学习

网上有很多学习 Clojure 和 ClojureScript 的资源。我觉得最好的是 [clojureacademy](https://clojurecademy.com/) 。这很像[代码学院](https://www.codecademy.com/)。高度互动的学习环境。缺点是它只包含 Clojure 上的课程，没有特定的 ClojureScript。

在阅读官方文档和第三方软件包文档时，我注意到这种语言比我习惯的更加华丽。还有其他人注意到了吗？

### 教程

*   **[ClojureAcademy](https://clojurecademy.com/)** 伟大的课程。互动性强，写得很好。
*   **[现代 ClojureScript](https://github.com/magomimmo/modern-cljs)**ClojureScript 广泛教程。

*   **[purely functional . TV](https://purelyfunctional.tv/)T3】**

### 文档

*   **[CLJS Api](http://cljs.github.io/api/)**

## 工作流程

如果你是从 webpack 和 NPM 的网络世界直接过来的，工作流程就不会那么流畅。ClojureScript 在 JVM 上运行，启动相当慢。但是，当它启动并运行时，不会出现任何问题。在为我建立一个网络 SPA 的同时，在 JVM 上工作仍然感觉迟钝和有点奇怪。Figwheel (ClojureScript 的 live reloader)真的是必须的，它的工作也非常完美。

ClojureScript 真正闪光的地方是 REPL 功能。这是一个强大的功能，并邀请您尝试，测试和学习。

[![Clojure tools](img/5ec0419e1ded15c383f7ff4989ef6371.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VPApoynK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mio2lrw2as2kuv5pqsw5.png)

## 第三方套餐

似乎在 ClojureScript 的社区中有许多高技能的开发人员。包装不是很多，但大多数质量都很好。作为初学者，我发现关于其中一些的文档相当缺乏。

您也可以使用第三方 javascript 库，或者通过 [CLJSJS](http://cljsjs.github.io/) 或者将它包含在您的构建过程中。

## 工装

[![Clojure tooling](img/c273f4f5392640f212e1fcb15327e3b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5qwL4JJK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqng1lq896o5mz5vq58c.png)

*   **[Atom](https://atom.io/)**
    *   可以很好地使用插件(pari fer，atom-pari fer，lisp-paredit)
    *   不是很好的开箱即用体验
*   **[草书](https://cursive-ide.com/)**
    *   商业许可证的费用是 199 美元
    *   伟大的代码完成，REPL，和调试器
    *   IntelliJ 插件
*   **[【vscode】](https://code.visualstudio.com/)**
    *   可以接受插件(Vs-code-par ifer，vscode-clojure-debug)
    *   插件仍然不成熟
*   **[光表](http://lighttable.com/)**
    *   对 ClojureScript 的一流支持(内置了看片台)
    *   许多插件
    *   缓慢的性能和并非完全没有错误
    *   发展似乎停滞了
*   **[Emacs](https://www.gnu.org/software/emacs/)**
    *   最受欢迎的选择
    *   许多插件
    *   陡峭的学习曲线
    *   基于键盘的工作流程
*   **[夜灯](https://sekao.net/nightlight/)**
    *   在您的项目中运行
    *   基本编辑器功能
    *   REPL、代码完成和 instaREPL
*   **[夜曲](https://sekao.net/nightcode/)**
    *   Clojure 的简单 IDE(脚本)
    *   易于上手
    *   也有基于网络的版本
*   **[Vim](https://www.vim.org/)T3】**
    *   相当受欢迎的选择
    *   基于键盘的工作流程

与其他语言相比，ClojureScript 的工具很弱。经过我短时间的测试，最好的无疑是 Emacs、草书和 Atom。还有其他不错的产品，如 nightlight 和 nightcode，但就功能而言，它们非常简单。

## 结论

我相信 ClojureScript 仍然有一些工作要做，所以对于通常使用 React、Vue 或 Angular 的 web 开发人员来说，学习它会更有吸引力。工具支持是必须的，因为大多数程序员都有自己喜欢的编辑器，并且需要时间来适应编辑器。文档很好，但是还没有。

*[图片取自 2016 年 Clojure 州调查](http://blog.cognitect.com/blog/2017/1/31/state-of-clojure-2016-results)*