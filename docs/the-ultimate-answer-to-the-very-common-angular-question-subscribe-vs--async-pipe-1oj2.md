# 最常见的角度问题的最终答案:subscribe() vs | async Pipe

> 原文：<https://dev.to/tomastrajan/the-ultimate-answer-to-the-very-common-angular-question-subscribe-vs--async-pipe-1oj2>

大多数流行的角度状态管理库，如 NgRx，以状态对象流的形式公开应用程序状态。这通常在 RxJS Observables 的帮助下实现。

> 状态更新被推送到组件，组件通过重新呈现它们的模板来显示应用程序状态的最新版本。

组件可以通过多种方式来使用这种可观察到的状态更新流，最常见的两种方式是:

*   使用`subscribe()`方法并在组件实例`todos$.subscribe(todos => this.todos = todos)`上存储状态...

*   异步管道直接在组件的模板中展开状态对象，`<li *ngFor=”let todo of todos$ | async”></li>`...

我一直在思考这个问题和相关的权衡。我很赞成使用 subscribe()，但是不能真正指出具体的原因。

[阅读更多...](https://medium.com/@tomastrajan/angular-question-rxjs-subscribe-vs-async-pipe-in-component-templates-c956c8c0c794)