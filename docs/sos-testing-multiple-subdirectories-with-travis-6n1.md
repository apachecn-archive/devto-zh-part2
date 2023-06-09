# SOS:用 Travis 测试多个子目录

> 原文：<https://dev.to/mmphego/sos-testing-multiple-subdirectories-with-travis-6n1>

这是给所有崔维斯忍者的

我有一个包含所有 docker 文件的 repo，我想让 Travis 单独对它们进行测试，但是它们都在不同的目录中。下面是我的目录结构。

```
GitHub repo
    |-> fast.ai
        |- .travis.yml
        |- Dockerfile
    |-> Jekyll
        |- .travis.yml
        |- Dockerfile
    |-> LaTex
        |- .travis.yml
        |- Dockerfile

    |-> .travis.yml 
```

Enter fullscreen mode Exit fullscreen mode

我可以运行一个 Travis 构建，但这只是将所有结果聚合到一个 Travis svg 中。

```
language: c
sudo: required

services:
    - docker
notifications:
  email: false

install:
  - make -C fast.ai build && make -C fast.ai run
  - make -C Jekyll build && make -C Jekyll run

before_install:
  - cd fast.ai && git clone --depth 1 https://github.com/fastai/fastai.git && cd -
  - cd Jekyll && git clone --depth 1 https://github.com/mmphego/mmphego.github.io && cd -

script:
- docker ps -a 
```

Enter fullscreen mode Exit fullscreen mode

问:有可能将 Travis 运行到多个目录并得到多个结果吗？
[![](img/dd01bc8cd4ae51eeaa3e8898eb8d0364.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--KNHjbwrf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ldun60megxzhj71i6zdc.jpg)