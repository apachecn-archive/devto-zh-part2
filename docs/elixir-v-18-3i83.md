# Elixir :下一个版本 v 1.8 中的新功能

> 原文：<https://dev.to/gumi/elixir-v-18-3i83>

Elixir v1.8 将于 2019 年 1 月发布。 在此之前，2018 年 11 月 16 日，在 Plataformatec 公司的博客上以“[what’s new in elixir–nov/18](http://blog.plataformatec.com.br/2018/11/whats-new-in-elixir-nov-18/)”为题，介绍了已经添加的主要新功能。 本文得到 Plataformatec 公司的许可，在本文的内容中加入 GitHub 的提交信息，进行整理说明。

*   在`mix test`的任务中添加`--max-failures`的标志。

    *   コミット「 [介绍 ExUnit - max-failures 功能](https://github.com/elixir-lang/elixir/commit/8c373044dcde1654670d8da10448e4b8fecf0b93)
    *   达到指定的 n 次失败后，套件将中止。 然后，返回的是到目前为止完成的测试的次数。 如果模块被禁用，则所有测试都将被计为失败，同时报告此情况。
*   `Inspect`协议变得容易提取。 该机制的作用是从检查和日志中排除特定字段，特别是关于安全相关的字段。

    *   コミット「 [允许使用唯一/例外选项](https://github.com/elixir-lang/elixir/commit/10ceadb448ba6a913f3070c3cc51c9c7be1f280b)导出检查协议
    *   `Inspect`可以用协议从结构体中隐藏特定的字段，使其不显示在检查或日志等中。 对于包含个人信息的字段等很有用。 在`@derive`中通过`:only`和`:except`选项确定显示或不显示的字段。
*   根据`:extra_applications`的设定，Mix 自动开始选项的依赖关系。

    *   コミット「 [在 extra_applications](https://github.com/elixir-lang/elixir/commit/53ebafb8a4bc2fe73caefd24b49c95cc2943a340) 中包含可选的依赖项
    *   到目前为止，`compile.app`不会自动包含在`extra_applications`中。
*   Elixir 将得到时区数据库的官方支持。 缺省情况下，只有 UTC 处理。 但是，通过其他数据库，可以在其他时区使用`DateTime`API。

    *   コミット「 [为时区数据添加时区数据库行为。](https://github.com/elixir-lang/elixir/commit/b8b1a4ba8af8256567f00e6d9f7fa056881a8ec1)
    *   其他时区数据库(包括软件包的数据库)可以在`config`中默认设置，也可以调用`Calendar.put_time_zone_database/1`使用。
*   其他附加内容包括以下函数:

    *   [T2`List.myers_difference/3`](https://github.com/elixir-lang/elixir/commit/2d7bd055ed0f8ee9fdb130bbd969033b705b4943)
    *   [T2`String.bag_distance/2`](https://github.com/elixir-lang/elixir/commit/76c144aa26301018b39a7b4d70be226034faac43)

有关所做变更的详细内容，请参考“[CHANGELOG](https://github.com/elixir-lang/elixir/blob/master/CHANGELOG.md) ”。 今后的变更和功能的添加可以在 Elixir 的“[Issues](https://github.com/elixir-lang/elixir/issues) ”中看到。