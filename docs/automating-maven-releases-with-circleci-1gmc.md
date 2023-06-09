# 使用 CircleCI 自动发布 Maven

> 原文：<https://dev.to/dmfay/automating-maven-releases-with-circleci-1gmc>

Maven 可能是我唯一真正欣赏过的一体化构建工具*。我很可能最终会喜欢上`make`并巩固我作为“她之前的老女人”的地位，但我还没有真正深究它的理由，所以它可能是。我又回到了一个只有 Java 的商店，所以让我们开心一下吧！*

 *本周的目标:从我们的 CircleCI 实例中自动化发布。听起来很简单，对吧？修改版本，剪下标签，出版。会有多难呢？

嗯，首先，我们使用 [git-flow](https://nvie.com/posts/a-successful-git-branching-model/) ，或者至少我们为发布保留了`master`，并开发了一个单独的`verify`分支。预算 git-flow，如果你愿意。这是一个复杂的问题，因为发布必须在`master`上标记，但`verify`也需要更新，这样两者就不会有分歧。

如果你熟悉 Maven，你可能已经猜到了第二个复杂之处。这更棘手。Maven 不能在简单明了的 [semver](https://semver.org) 中工作:Maven 接受几种不同的版本控制方案，并且有一个针对非发布版本的特殊的`SNAPSHOT`限定符。如果您正在开发 1.0 版本，那么您的版本号就是 1.0-快照。在您剪切了发布版本之后，您可以使用 1.1-SNAPSHOT(或者 2.0-SNAPSHOT，如果它确实已经需要返工的话)继续开发。诸如此类。这并不是说*意味着*要自动化，因为发布在 Maven 世界里是一件*大事*,你应该对下一步做什么有一个计划，而不是对你是否修复了 bug、引入了特性或者破坏了兼容性做出反应。老实说，这样做有一些令人信服的理由。

我不打算深入讨论它们，因为我自己是软件团队的一员，它们不太适用于这种规模的专有工作。所以让我们开始自动化吧！

## 工作流程

我们使用 Circle v2 及其工作流特性来组织构建。每个分支都被建立:`verify`和`master`被部署到 Artifactory，而`release`触发自己的工作，后者是整个结构的关键。

```
workflows:
  version: 2
  build-and-deploy:
    jobs:
      - build
      - deploy:
          requires:
            - build
          filters:
            branches:
              only: /^(master|verify)$/
      - release:
          requires:
            - build
          filters:
            branches:
              only: /^release$/ 
```

Enter fullscreen mode Exit fullscreen mode

## 刚刚建成

老实说，我直接从文档中复制并粘贴了这个工作定义的大部分内容:

```
steps:
  - checkout
  - restore_cache:
      keys:
      - v1-dependencies-{{ checksum "pom.xml" }}
      # fallback to using the latest cache if no exact match is found
      - v1-dependencies-
  - run: mvn clean install
  - save_cache:
      paths:
        - ~/.m2
      key: v1-dependencies-{{ checksum "pom.xml" }}
  - persist_to_workspace:
      <<: *source 
```

Enter fullscreen mode Exit fullscreen mode

我们缓存我们的依赖关系，因为这是一个人应该做的；`mvn clean install`很可能是多余的(我们可能不需要费心将依赖项安装到本地 Maven 缓存中)，但是它构建并运行了我们的测试并生成了工件。这里唯一真正有趣的部分是，我们将重要的文件持久化到一个工作区，这样我们可以在以后恢复它- `*source`引用另一个 YAML 块，带有一个`root`字符串和一个`paths`列表。

## 并部署

```
steps:
  - attach_workspace:
      at: .
  - run:
      name: Deploy to Artifactory
      command: mvn deploy 
```

Enter fullscreen mode Exit fullscreen mode

这是我们使用工作空间的地方。每当这个作业运行时，它将重新附加我们从构建作业中保存的文件结构。仍然运行所有的中间生命周期阶段，因为这是 Maven 的方式，但是我们不需要再次检查代码。

我们已经用 [artifactory-maven-plugin](https://www.jfrog.com/confluence/display/RTF/Maven+Artifactory+Plugin) 设置了 POMs，所以我们要做的就是发布`mvn deploy`。至少，这让事情变得简单了；如果你愿意，还有 Artifactory CLI，但是 Maven 的整个交易是管理一切，所以就我而言，我们应该让它这样做。

然而，只缺少了一个部分:我们如何实际发布工件的新版本并开始下一个版本？

## 释放触发器

git-flow 的一个想法是，当你为一个发布做好准备时，你就切断了一个新的分支，这个分支只包含那个发布的工作。如果您同时处理多个版本的代码，并且发布可能需要一段时间，那么这是非常好的，因此您可以从当前开发中挑选一个 bugfix 到遗留发布分支中，以确保它不会影响您的用户子集。由于我们不是一家产品公司，我们真的不必担心这一点。我们一直在努力开发下一个版本，当它准备好要发布的时候就会发布。

这将变得复杂。下面是完整的`release`构建步骤:

```
steps:
  - checkout
  - run:
      name: Cut new release
      command: |
        # assemble current and new version numbers
        OLD_VERSION=$(mvn -s .circleci/settings.xml -q \
          -Dexec.executable="echo" -Dexec.args='${project.version}' \
          --non-recursive org.codehaus.mojo:exec-maven-plugin:1.3.1:exec)
        NEW_VERSION="${OLD_VERSION/-SNAPSHOT/}"
        echo "Releasing $OLD_VERSION as $NEW_VERSION"

        # ensure dependencies use release versions
        mvn -s .circleci/settings.xml versions:use-releases

        # write release version to POM
        mvn -s .circleci/settings.xml versions:set -DnewVersion="$NEW_VERSION"

        # setup git
        git config user.name "Release Script"
        git config user.email "builds@understoryweather.com"

        # commit and tag
        git add pom.xml
        git commit -m "release: $NEW_VERSION"
        git tag "$NEW_VERSION"

        # land on master and publish
        git checkout master
        git merge --no-edit release
        git push origin master --tags

        # increment minor version number
        MAJ_VERSION=$(echo "$NEW_VERSION" | cut -d '.' -f 1)
        MIN_VERSION=$(echo "$NEW_VERSION" | cut -d '.' -f 2)
        NEW_MINOR=$(($MIN_VERSION + 1))
        DEV_VERSION="$MAJ_VERSION.$NEW_MINOR-SNAPSHOT"

        # ready development branch
        git checkout verify
        git merge --no-edit release
        mvn -s .circleci/settings.xml versions:set -DnewVersion="$DEV_VERSION"
        git add pom.xml
        git commit -m "ready for development: $DEV_VERSION"
        git push origin verify

        # clean up release branch
        git push origin :release 
```

Enter fullscreen mode Exit fullscreen mode

不是*乱*，而是那种...很多 bash 脚本。但是就像任何足够复杂的数据库任务都需要编写 SQL 一样，任何足够复杂的 ops 任务都需要 bash。让我们来分解一下:

### 获取版本号

```
# assemble current and new version numbers
OLD_VERSION=$(mvn -s .circleci/settings.xml -q \
  -Dexec.executable="echo" -Dexec.args='${project.version}' \
  --non-recursive org.codehaus.mojo:exec-maven-plugin:1.3.1:exec)
NEW_VERSION="${OLD_VERSION/-SNAPSHOT/}"
echo "Releasing $OLD_VERSION as $NEW_VERSION" 
```

Enter fullscreen mode Exit fullscreen mode

注意`-s .circleci/settings.xml`:因为 Circle 只是旋转了一个基本的 OpenJDK 映像，所以我们将`settings.xml`签入了源代码控制。凭证是通过环境变量内插的，但还是没有*伟大*；在某个时候，我会想回来创建一个定制的 Docker 映像来集中我们的配置。

Maven 将版本号存储在 POM 中。我们可以用 XPath 把它们取出来，但是因为这是 Maven，所以有一个插件。`OLD_VERSION`是当前值；由于我们总是从`verify`分支发布，这肯定是一个快照版本，我们需要去掉那个限定符来获得发布的`NEW_VERSION`。

### 更新版本

```
# ensure dependencies use release versions
mvn -s .circleci/settings.xml versions:use-releases

# write release version to POM
mvn -s .circleci/settings.xml versions:set -DnewVersion="$NEW_VERSION" 
```

Enter fullscreen mode Exit fullscreen mode

我们没有太多的 Java 库，但是有足够多的，发布管理(显然)是一个问题。这里的第一个声明确保了当我们发布时，我们不依赖于另一个库的快照版本。第二个实际上将 POM 中的 version 字段设置为我们刚才生成的发布版本。

你可能会问:为什么我不把`mvn`别名为`mvn -s .circleci/settings.xml`？而答案是:我做了，花了半天时间想明白为什么不行。我不知道是这个特定的图像还是这个圈子，但别名就是被忽略了。

### 发布！

```
# setup git
git config user.name "Release Script"
git config user.email "builds@understoryweather.com"

# commit and tag
git add pom.xml
git commit -m "release: $NEW_VERSION"
git tag "$NEW_VERSION"

# land on master and publish
git checkout master
git merge --no-edit release
git push origin master --tags 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将提交代码，所以我们需要做更多的 git 配置来正确地属性化提交。这是另一个我稍后可以用定制构建映像简化的元素。

接下来，我们提交更新的 POM 并创建一个标签。当我们合并时(因为脚本不能改变提交消息，所以与`--no-edit`),释放提交和标记将到达`master`分支。那么就只是推到原点的问题了。

### 接下来...

我们已经发布了，但还没有完全完成。如果我们把它留在这里，`verify`分支的下一个版本将会遇到合并冲突，因为`master`在 POM 中有一个更新的版本。为了防止这种情况，我们必须将*合并回`verify`* 。最好带有快照版本限定符，因为 Maven。

```
# increment minor version number
MAJ_VERSION=$(echo "$NEW_VERSION" | cut -d '.' -f 1)
MIN_VERSION=$(echo "$NEW_VERSION" | cut -d '.' -f 2)
NEW_MINOR=$(($MIN_VERSION + 1))
DEV_VERSION="$MAJ_VERSION.$NEW_MINOR-SNAPSHOT" 
```

Enter fullscreen mode Exit fullscreen mode

为了方便起见，我将我们切换到两部分版本号。因为 Maven 希望您知道自己在朝着什么方向努力，所以从 1.0 到 1.1 比试图弄清楚接下来是 1.0.1 还是 1.1.0 要现实得多。如果我们决定下一个版本实际上应该是 2.0，我们总是可以自己更新版本，但是我在这里尽量减少人工参与。

```
# ready development branch
git checkout verify
git merge --no-edit release
mvn -s .circleci/settings.xml versions:set -DnewVersion="$DEV_VERSION"
git add pom.xml
git commit -m "ready for development: $DEV_VERSION"
git push origin verify 
```

Enter fullscreen mode Exit fullscreen mode

将`release`合并到`verify`中可以避免任何潜在的合并冲突，因为相同的释放提交现在同时存在于`master`和`verify`中。然后，该脚本使用新的快照版本向`verify`添加第二次提交，并将其全部发送到原点。

```
# clean up release branch
git push origin :release 
```

Enter fullscreen mode Exit fullscreen mode

最后:当一个触发器启动时，它会重置。我们不希望分支机构长期存在。如果我们这样做了，我们将不得不把发布提交推到原点以避免将来的合并冲突，这样做将会引发一个无限循环，因为`release` *作业*正在监视这个分支。所以我们只是把它从原点删除，因为它已经做了所有需要做的事情。

## 关机

```
git checkout -b release
git push origin release 
```

Enter fullscreen mode Exit fullscreen mode

这就是回报。每当我们准备删除一个新版本时，所要做的就是创建一个名为`release`的新分支。如果你愿意的话，你甚至可以通过 GitHub 用户界面来完成，只需两次点击和七个字母。一旦`release`构建并删除了自己，普通的构建和部署任务将接管更新后的`master`和`verify`分支。几分钟之内，我们就发布了一个版本，并为 Artifactory 的下一次登陆发布了第一张快照！*