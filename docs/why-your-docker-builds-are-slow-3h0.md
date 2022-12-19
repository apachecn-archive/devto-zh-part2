# 为什么你的 docker 构建很慢？

> 原文：<https://dev.to/vladimir_vg/why-your-docker-builds-are-slow-3h0>

使用 docker 时，重要的是按照正确的顺序编写 Dockerfile 指令。这可能会大大加快构建速度。

Dockerfile 中的每个指令都类似于 git 分支中的一个 commit。如果你在中间修改了一些东西，那么后面的所有东西都应该被重新执行(用 git 术语来说)。改变之前的一切都可以重用。

使用 COPY 或 add 添加的所有文件都被视为随后运行的所有文件的依赖项。

因此，有两条规则可以加快你的形象建设:

*   哪些变更很少应该首先执行
*   避免使用“添加”它让一切都依赖于一切

添加 Gemfile、rebar.config、mix.exs 或依赖项指定的任何其他文件。安装依赖项，编译它们。并且只有之后的**才添加你的 app 源代码。**