# 为 Travis CI 搭建舞台

> 原文：<https://dev.to/kengotoda/build-stage-on-travis-ci-3pdk>

今天我发现[一篇关于 Travis CI 构建阶段的 Travis CI 博文](https://blog.travis-ci.com/2018-07-18-build-stages-officially-released)。现在正式上市，我们都可以享受这一功能。

这里是[我的公关介绍建立阶段](https://github.com/KengoTODA/findbugs-slf4j/pull/118/files)。在我看来，它有一个优点:

## 分析和部署部分不再有`if`

当我运行类似 [SonarCloud](https://sonarcloud.io) 的分析时，有必要限制目标 JVM &环境变量，以使 PR 页面易于阅读。例如，下面是一个`.travis.yml`片段，它只在特定条件下运行分析:

```
jdk:
  - oraclejdk8
  - oraclejdk9
script:
  - ./mvnw org.jacoco:jacoco-maven-plugin:prepare-agent verify -B
  - if [[ $TRAVIS_JDK_VERSION == "oraclejdk8" ]]; then ./mvnw sonar:sonar -B; fi 
```

Enter fullscreen mode Exit fullscreen mode

在构建阶段，脚本默认使用`env`、`jdk`和其他中的第一个值运行。所以我们可以把`if`从`.travis.yml` :
中去掉

```
jdk:
  - oraclejdk8
  - oraclejdk9
script:
  - ./mvnw verify -B
jobs:
  include:
    - stage: analysis
      script:
        - ./mvnw org.jacoco:jacoco-maven-plugin:prepare-agent verify sonar:sonar -B 
```

Enter fullscreen mode Exit fullscreen mode

一点是，每个构建阶段和作业不共享生成的文件。所以这里我们需要运行`verify`阶段，甚至在`analysis`阶段，来生成分析目标(这里是`.class`文件)。

这个功德工程甚至进行到[部署](https://docs.travis-ci.com/user/deployment)阶段。这个特性应该会降低我们的`.travis.yml`的复杂性。

仅此而已。享受与特拉维斯 CI 黑客！