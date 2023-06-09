# 我们如何使用 CircleCI 在 Instabug 自动化我们的 iOS 工作流程

> 原文：<https://dev.to/instabug/how-we-automate-our-ios-workflow-at-instabug-using-circleci-50n3>

#### 作者[赫沙姆·阿卜德·艾尔梅吉德](https://twitter.com/heshammegid)

在 Instabug，我们为移动应用开发了一个 SDK。构建 SDK 通常意味着大多数为围绕 iOS 应用开发和部署的自动化任务而创建的工具对我们不起作用，所以我们通常需要开发自己的定制解决方案。

为了提高我们生产力并保持事情快速发展，我们喜欢自动化。使我们工作流程的许多部分自动化的一个重要组成部分是持续集成。

这篇文章介绍了我们的工作流程，以及我们如何使用 [CircleCI](https://instabug.com/blog/continuous-integration-tools/?utm_source=devto&utm_medium=referral&utm_content=circleci) 实现自动化。

## 概述

使用 [CircleCI 2.0](https://circleci.com/) ，我们可以在一个工作流下创建多个作业，能够定义作业之间的依赖关系，并并行运行其中的一些作业，这非常棒。

```
workflows:
  version: 2
  build-test-and-generate-binary:
    jobs:
      - unit-tests
      - uitests-analyzer:
          filters:
            branches:
              only: 
                - master
                - /fix\/.*/
                - /release\/.*/
                - /Release\/.*/
      - hold:
          type: approval
          requires:
            - unit-tests
            - uitests-analyzer
      - generate-binary:
          requires:
            - hold
      - release:
          requires:
            - generate-binary
          filters:
            branches:
              only: 
                - master 
```

我们的工作流程有 4 项任务:

1.  运行单元测试、集成测试和危险
2.  运行 UI 测试和静态分析器
3.  生成二进制
4.  发布 SDK

让我们详细地看一下每一个。

## 1。运行单元测试、集成测试和危险

```
unit-tests:
  macos:
    xcode: "9.3.1"
  working_directory: /Users/distiller/project
  environment:
    BUNDLE_PATH: vendor/bundle  # path to install gems and use for caching
    ARTIFACTS_DIRECTORY: /Users/distiller/project/artifacts
  steps:
    - checkout
    - restore_cache:
        keys:
        - gems-{{ checksum "Gemfile.lock" }}
        # Fall back to using the latest cache if no exact match is found.
        # - v1-gems-
    - run: 
        name: Create artifacts directory
        command: mkdir $ARTIFACTS_DIRECTORY
    # Install gems.
    - run:
        name: Bundle install
        command: bundle check || bundle install
        environment:
          BUNDLE_JOBS: 4
          BUNDLE_RETRY: 3
    - save_cache:
        key: gems-{{ checksum "Gemfile.lock" }}
        paths:
          - vendor/bundle
    - run:
        name: Pre-start simulator
        command: xcrun instruments -w "iPhone 8 (11.3) [" || true
    - run:
        name: Run Instabug unit tests
        command: set -o pipefail && xcodebuild -workspace Instabug/Instabug.xcworkspace -scheme AllUnitTests -sdk iphonesimulator -destination 'platform=iOS Simulator,OS=11.3,name=iPhone 8' -enableCodeCoverage YES test | xcpretty --color --report junit --output $ARTIFACTS_DIRECTORY/AllTests_unittest_results.xml
    - run:
        name: Run Danger
        command: bundle exec Danger
    - run: 
        name: Run integration tests
        command: set -o pipefail && xcodebuild -workspace Instabug/Instabug.xcworkspace -scheme InstabugIntegrationTests -sdk iphonesimulator -destination 'platform=iOS Simulator,OS=11.3,name=iPhone 8' -enableCodeCoverage YES test | xcpretty --color --report junit --output $ARTIFACTS_DIRECTORY/InstabugIntegrationTests_unittest_results.xml 
```

首先，我们安装我们的项目使用的所有 gem，像 [Danger](http://danger.systems/) 和 [fastlane](https://fastlane.tools/) 或者通过恢复先前安装的缓存，或者通过全新安装。

然后我们运行我们的单元测试。由于我们项目的结构，我们有大约 15 个框架目标，每个都有一个单独的测试目标。为了一次运行所有的单元测试，我们有一个运行所有测试目标的`AllUnitTests`方案。

在运行完单元测试之后，我们运行 Danger，这是一个很好的工具，可以自动完成一些代码审查的杂务。这是我们现在使用它的目的。

1.  对于所有拉取请求，Enforce having 都有一个吉拉问题的描述和链接
2.  确保所有添加面向用户的新字符串的拉请求都会添加这些字符串的本地化版本。
3.  运行 [xcov](https://github.com/nakiostudio/xcov) 来生成一个关于我们的测试覆盖率的报告，并将其作为 pull 请求的注释发布
4.  确保修改任何 UI 文件的所有 pull 请求在合并 pull 请求之前运行 UI 测试。

```
has_ui_changes = !git.modified_files.grep(/View Controllers/).empty? || !git.modified_files.grep(/Views/).empty?

if !ENV['RUN_UI_TESTS'] && has_ui_changes
    fail("UI has been changed but UI tests were not run. Please make sure to run them before merging the PR.")
end

# Make sure PR has a description.
if github.pr_body.length < 3 && git.lines_of_code > 10
    fail "Please provide a summary of the changes in the Pull Request description."
end

# Check if PR title has reference to a Jira issue.
if !github.pr_title[/\[[a-zA-Z]*-[0-9]*\]/]
    fail("Pull request should include Jira card number in the name. For example: [IBGProj-123]")
end 
```

这项工作的最后一步是运行我们的集成测试，这只是一个测试目标，与我们的单元测试目标具有相似的配置。

这个作业在每个拉请求上运行，所以我们必须确保它包含所有必要的检查/测试，并且它也在合理的时间内运行。它目前运行大约需要 8 分钟，大部分时间都在为运行测试进行一个干净的 SDK 构建。

## 2。运行 UI 测试和静态分析器

```
uitests-analyzer:
  macos:
    xcode: "9.3.1"
  working_directory: /Users/distiller/project
  environment:
    BUNDLE_PATH: vendor/bundle  # path to install gems and use for caching
    ARTIFACTS_DIRECTORY: /Users/distiller/project/artifacts
  steps:
    - checkout
    - restore_cache:
        keys:
        - gems-{{ checksum "Gemfile.lock" }}
        # Fall back to using the latest cache if no exact match is found.
        # - v1-gems-
    - run: 
        name: Create artifacts directory
        command: mkdir $ARTIFACTS_DIRECTORY
    # Install gems.
    - run:
        name: Bundle install
        command: bundle check || bundle install
        environment:
          BUNDLE_JOBS: 4
          BUNDLE_RETRY: 3
    - run:
        name: Pre-start simulator
        command: xcrun instruments -w "iPhone 8 (11.3) [" || true
    - run:
        name: Run UI tests
        command: |
          if [[ -n "${RUN_UI_TESTS}" || $CIRCLE_BRANCH = 'master' ]]; then
            set -o pipefail && xcodebuild -workspace Instabug/Instabug.xcworkspace -scheme InstabugDemoUITests -sdk iphonesimulator -destination 'platform=iOS Simulator,OS=11.3,name=iPhone 8' -only-testing:InstabugDemoUITests/AlertsUITests -only-testing:InstabugDemoUITests/BugReportingDisabledAttachmentsUITests -only-testing:InstabugDemoUITests/BugReportingUITests -only-testing:InstabugDemoUITests/IBGAnnotationUITests -only-testing:InstabugDemoUITests/IBGChatBasicUITests -only-testing:InstabugDemoUITests/IBGPromptVCUITests -only-testing:InstabugDemoUITests/IBGStatusBarTests test | xcpretty --color --report junit --output $ARTIFACTS_DIRECTORY/xcode/uitest_results_1.xml
            set -o pipefail && xcodebuild -workspace Instabug/Instabug.xcworkspace -scheme InstabugDemoUITests -sdk iphonesimulator -destination 'platform=iOS Simulator,OS=11.3,name=iPhone 8' -only-testing:InstabugDemoUITests/IBGSurveysFlowUITests -only-testing:InstabugDemoUITests/IBGSurveysUITests -only-testing:InstabugDemoUITests/InstabugUITests -only-testing:InstabugDemoUITests/IBGFeatureRequestUITests test | xcpretty --color --report junit --output $ARTIFACTS_DIRECTORY/xcode/uitest_results_2.xml
          else
            echo 'Skipping running UI Tests.'
          fi
        no_output_timeout: 15m

    - run:
        name: Run static analyzer on other frameworks
        command: sh ./Scripts/analyze_Instabug.sh 
```

这项工作与我们在第一项工作中所做的非常相似，但是它使用`xcodebuild analyze`运行 UI 测试目标和静态分析器。

这个作业只在主分支和修复/发布分支上运行，所以我们可以接受它需要多一点时间来运行。它目前大约在 40 分钟内完成，并与第一个作业并行运行。它还可以在任何 pull 请求上按需运行，而不管它的分支是什么，只需提到我们编写的一个简单的 GitHub bot，它使用 CircleCI API 来触发特定的作业。

## 3。生成二进制

```
generate-binary: 
  macos:
    xcode: "9.3.1"
  working_directory: /Users/distiller/project
  environment:
    BUNDLE_PATH: vendor/bundle  # path to install gems and use for caching
    ARTIFACTS_DIRECTORY: /Users/distiller/project/artifacts
  steps:
    - checkout
    - restore_cache:
        keys:
        - gems-{{ checksum "Gemfile.lock" }}
        # Fall back to using the latest cache if no exact match is found.
        # - v1-gems-
    - run: 
        name: Create artifacts directory
        command: mkdir $ARTIFACTS_DIRECTORY
    # Install gems.
    - run:
        name: Bundle install
        command: bundle check || bundle install
        environment:
          BUNDLE_JOBS: 4
          BUNDLE_RETRY: 3
    - run:
        name: Install signing identity
        command: |
          bundle exec fastlane setup_signing
    - run:
        name: Increment version number
        command: |
          ./Scripts/IncrementSDKVersion.swift
          /usr/libexec/PlistBuddy -c "Set :CFBundleVersion $CIRCLE_BUILD_NUM" "Instabug/InstabugI/Info.plist"
    - run:
        name: Generate fat binary for Instabug static
        command: |
          xcodebuild -workspace Instabug/Instabug.xcworkspace -scheme Framework -sdk iphoneos -destination generic/platform=iOS clean build | xcpretty
    - run:
        name: Link Instabug static with dynamic project
        command: |
          ruby ./Instabug-dynamic/linkInstabug.rb
    - run:
        name: Generate fat binary for Instabug dynamic
        command: |
          xcodebuild -project Instabug-dynamic/Instabug.xcodeproj -scheme Framework -sdk iphoneos -destination generic/platform=iOS clean archive | xcpretty
    - run:
        name: Generate appledoc
        command: |
          sh ./Scripts/generate_appledoc.sh
    - run:
        name: Create framework archive
        command: |
          find ./Instabug/Instabug-SDK-Static -path '*/.*' -prune -o -type f -print | zip $ARTIFACTS_DIRECTORY/Instabug-static.zip -@
          find ./Instabug-dynamic/Instabug-SDK -path '*/.*' -prune -o -type f -print | zip $ARTIFACTS_DIRECTORY/Instabug.zip -@
          find ./Instabug-Docs -path '*/.*' -prune -o -type f -print | zip $ARTIFACTS_DIRECTORY/appledoc.zip -@
    - run:
        name: Test Fat Binaries are not Corrupted
        command: |
          xcodebuild -project InstabugProductionDemo/InstabugProductionDemo.xcodeproj -scheme InstabugProductionDemoUITests -sdk iphonesimulator -destination 'platform=iOS Simulator,OS=11.3,name=iPhone 8' test | xcpretty
    - store_artifacts:
        path: artifacts
    - persist_to_workspace:
        root: .
        paths:
          - . 
```

前两个任务完成后，我们可以从任何分支生成 SDK 的二进制文件。这需要 CircleCI 仪表板的批准。

这项工作将安装我们的代码签名身份，我们使用 [fastlane](https://docs.fastlane.tools/actions/match/) 在整个团队中共享该身份。然后，它会生成我们框架的静态和动态变体。关于构建二进制框架的更多信息，请查看我们之前的[博客文章](https://instabug.com/blog/ios-binary-framework/?utm_source=devto&utm_medium=referral&utm_content=circleci)。

然后，它将两个框架打包在一个 zip 存档中，并存储在工件目录中以供下载。

## 4。发布 SDK

```
release:
  macos:
    xcode: "9.3.1"
  working_directory: /Users/distiller/project
  environment:
    ARTIFACTS_DIRECTORY: /Users/distiller/project/artifacts
  steps:
    - attach_workspace:
        at: /Users/distiller/project
    - run:
        name: Release
        command: ./release 
```

最后一个作业是向公众发布 SDK。它运行我们自己开发的用于发布 SDK 的 CLI 应用程序“释放”。

释放会执行以下操作:

1.  创建一个新标签并将其推送到我们的私有存储库中
2.  将更新后的框架推送到 https://github.com/Instabug/Instabug-iOS/，并创建一个 GitHub 版本
3.  将框架发布到 CocoaPods
4.  更新我们的迦太基 [JSON 文件](https://github.com/Instabug/Instabug-iOS/blob/master/Instabug.json)
5.  将发布上传到我们自己的 API，以便在 Instabug 仪表板和网站上使用。

## 结论

我们对目前的工作流程非常满意。拥有一个可靠的 CI 流程使我们可以放心发货，并且自动化我们的发布流程节省了大量时间，因为我们每周发布一次。

CircleCI 是我们持续集成的一个很好的工具，尤其是 CircleCI 2.0。

**在 instabug.com/blog[了解更多信息](https://www.instabug.com/blog/?utm_source=devto&utm_medium=referral&utm_content=circleci)。**