# 测试覆盖范围:интеграция代码气候и Travis CI

> 原文：<https://dev.to/phpprofi/test-coverage--codeclimate--travis-ci-151d>

当您开发一个开源项目时，拥有高测试复盖率被认为是一个好的做法，这样社区就可以在您的项目中使用您的代码感到安全。有在线服务可以分析您的代码质量，并提供报告和建议。其中最受欢迎的是 t0code climate t1。此服务不会运行您的测试，但您可以使用其中一个连续集成(CI)工具来运行这些测试，然后将结果发送到 Code Climate。本文将说明如何使用 T2 Travis ci T3 运行测试，以及如何使用 T4 codeclimate T5 中的测试复盖率数据。

[![logo](img/dd0382b68a2bc5ccf272f4b9380d5791.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wmlks2-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://sergeyzhuk.me/asseimg/posts/travisci-codeclimate/travis-loves-code-climate.png)

* * *

http://phpprofi.ru/blogs/post/98 的читать

* * *

http://sergeyzhuk.me/2018/01/11/travis-with-codeclimate/