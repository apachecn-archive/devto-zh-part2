# 服务器端渲染如 Next。JS 副作用与保护路线

> 原文：<https://dev.to/codeprototype/server-side-rendering-such-as-nextjs-side-effects-with-protecting-routes-3knb>

服务器端渲染如 Next。JS 带来了很多好处，但是当涉及到保护路线时，它也带来了自己的问题。例如，泰勒·麦金尼斯的方法就行不通。另一个例子是简单地检查 *localStorage* 中的一些属性在 *getInitialProps* 中不起作用，因为 *localStorage* 是 NodeJS 上不存在的全局*窗口*对象的属性。如果你知道其他人，你介意分享吗？