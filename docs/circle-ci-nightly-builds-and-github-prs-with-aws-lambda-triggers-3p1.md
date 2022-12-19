# 使用 AWS Lambda 触发器循环 CI 夜间构建和 Github PRs

> 原文：<https://dev.to/rob117/circle-ci-nightly-builds-and-github-prs-with-aws-lambda-triggers-3p1>

嗨！在这篇博文中，我将向您详细展示如何使用 AWS Lambda 在 CircleCI 中创建每日/每夜的构建，并获取这些构建的结果，然后创建一个包含更改的 Github pull 请求。本教程的所有内容都是 100%免费的。

[此处储存库](https://github.com/Rob117/CircleCI-Nightly-Builds)

对于这个项目，我假设你已经在 CircleCI 和 Github 上有了你的项目。如果你不使用 Github，Lambda 和 CircleCI 部分对你仍然有用，只需忽略我们创建 pull 请求的最后部分。如果你没有 CircleCI，很遗憾这个项目对你几乎没有任何帮助，除非你想看一个 Github API 请求的例子。

注意:在 bash 脚本中，我们使用行`git add apples.txt`来添加我们最新的文件。我建议写`git add <whatever your files are expected to be>`而不是像`git add .`这样的表达。例如，如果您运行了 bundle update，我会建议您只运行`git add Gemfile.lock`。这是因为 CircleCI 在为事情做准备时，会以疯狂的方式干扰你的回购。示例:在它到达您的代码之前，它可能会运行类似 bundle install 的东西来获取您的所有项目依赖项。这意味着你将突然拥有大量与供应商/捆绑包中的 gems 相关的文件，如果你没有忽略这些文件，你将看到一个巨大的差异。还可以编辑 database.yml，就这样。

在这个过程中，我将解释一些疑难杂症或奇怪的代码行。我们开始吧。

每夜构建的工作方式是，首先，我们将创建一个 bash 脚本来运行我们想要在构建机器上执行的所有代码(在本例中，创建一个文件)。这可以是从`rails t`到`bundle update`的任何东西，也可以是检查新加坡服务器周围天气的 bash 脚本(如果你写了，请给我看看，我会非常感兴趣)。该脚本的结尾将让我们把我们对 Github 所做的任何更改作为一个 pull 请求。

然后，我们将把脚本放在 CircleCI 文件中，以便在通过 API 触发构建时激活它。这个比较简单，几行。

最后，我们将构建一个 lambda，并在其上添加一个 cron 作业，让它以您喜欢任何时间和频率继续我们的构建。注意:cron 被限制在不大于 1/分钟的速率[(来源)](http://docs.aws.amazon.com/lambda/latest/dg/tutorial-scheduled-events-schedule-expressions.html)。

首先，bash 脚本。创建一个文件名描述代码将做什么的文件。我写了一个名为 bundle_update.sh 的脚本，因为我们要更新 bundle。别忘了 chmod +x！

[bundle_update.sh](https://github.com/Rob117/CircleCI-Nightly-Builds/blob/master/bundle_update.sh)

```
 # First check out the branch you want to work code magic on. This is our base.
git checkout 'master'
# Force our branch to be in alignment with remote.
git reset --hard origin/master
# Make sure the commit is made with the correct credentials
git config --global user.email "<Your email here>"
git config --global user.name "circle-ci"
# Check to see if our work branch exists - $? command will return 0 if branch exists
# of course, this branch name could be anything you like, it's just important that it is unique to this script.
git rev-parse --verify circle_ci/make_apples
# If we have no branch, make one. Else, switch to it.
if ! [ 0 == $? ]; then
  git checkout -b 'circle_ci/make_apples'
else
  git checkout 'circle_ci/make_apples'
fi

# Run your custom code here. We'll just make a file for ease of use.
# This code can be anything you want to do, bundle update, nginx ip configs, etc.
touch apples.txt

# If we have changes, make a PR for those changes
if [[ `git status --porcelain` ]]; then
  # Ensure that our branch is correct. The last thing you want is to force push into the wrong branch
  if [ `git rev-parse --abbrev-ref HEAD` == 'circle_ci/make_apples' ]; then
    git add 'apples.txt'
    git commit -m 'My commit message here'
    # -f Necessary to possibly overwrite any existing remote branch code and force this to be correct
    # -u sets upstream for circleci
    git push -f -u origin circle_ci/make_apples
    # Head = Code to merge, base = branch to merge into
    # $GITHUB_ACCESS_TOKEN is an environment variable we set on CircleCI, but you must set it on your local machine if you want to test there as well
    # Example Github URL: https://api.github.com/repos/Rob117/circletest/pulls?access_token=$GITHUB_ACCESS_TOKEN
    curl -H "Content-Type: application/json" -X POST -d '{"title":"Title Of Pull Request Here","body":"Body of pull request here", "head": "circle_ci/make_apples", "base":"master"}' https://api.github.com/repos/<Organization or User Name>/<Project Name>/pulls?access_token=$GITHUB_ACCESS_TOKEN
  fi
fi 
```

Enter fullscreen mode Exit fullscreen mode

在我解释上面代码的陷阱之前，请访问 Github 并创建一个[令牌](https://github.com/settings/tokens)。该令牌应勾选 repo 复选框，因此它有`Full control of private repositories`。保存那个令牌。切换到 CircleCI，在环境变量下的项目设置页面上，创建一个名为 GITHUB_ACCESS_TOKEN 的变量，其值可以是您的令牌字符串。

还要确保你的项目也有 Github 的 ssh 权限，否则我们无法从脚本中推送代码。您可以通过检查项目设置中的 ssh 密钥来进行检查，并确保您有一个允许 SSH 到 git repo 的密钥。如果没有(即您没有用户密钥)，请单击添加用户密钥并授权应用程序使用您的密钥。授权后，点击“创建和添加用户密钥”按钮，在机器上获取您的 ssh 密钥，以便它们可以推送代码。

如果您想在您的本地机器上测试代码，请继续操作`export GITHUB_ACCESS_TOKEN=<string here>`。

好了，现在我们可以运行这段代码了，让我们来分解一下。

大部分评论都把代码解释清楚了。记得在 git config 部分替换你的电子邮件，在最后一行替换你的 Github API URL organziation name 和 project name。这些问题是:

我们必须使用`git reset --hard origin/develop`将我们的基本分支重置为远程上的任何内容。这样做的原因是，作为使用 CircleCI 的副产品，在我们执行一行代码之前，我们的构建机器存储库会发生很多事情，它们可能会影响最终结果。将 develop 与它的远程副本同步可以确保我们有一个原始的副本，所以这是一个很好的实践。

这两行:

```
git rev-parse --verify circle_ci/make_apples
if ! [ 0 == $? ]; then 
```

Enter fullscreen mode Exit fullscreen mode

第一行检查 git 分支是否存在。如果是这样，当您运行命令$？，否则它会显示别的东西。这告诉我们分支是否存在，这样我们就知道是否要创建一个分支。

```
if [[ `git status --porcelain` ]]; 
```

Enter fullscreen mode Exit fullscreen mode

简单地运行 git status，如果没有变化，则不输出任何内容。如果输出文本，我们希望发出一个拉请求。

```
if [ `git rev-parse --abbrev-ref HEAD` == 'circle_ci/make_apples' ]; then 
```

Enter fullscreen mode Exit fullscreen mode

检查我们的分支是否被正确创建。我们这样做是为了确保我们不会强制推送到错误的分支。

```
curl -H "Content-Type: application/json" -X POST -d '{"title":"Title Of Pull Request Here","body":"Body of pull request here", "head": "circle_ci/make_apples", "base":"master"}' https://api.github.com/repos/Rob117/circletest/pulls?access_token=$GITHUB_ACCESS_TOKEN 
```

Enter fullscreen mode Exit fullscreen mode

这就是神奇之处。Base 是我们开始时使用的分支，我们希望它反映合并后的代码变化。Head 是我们在这个脚本中创建的执行所有工作的分支。我们接受更改，并使用存储在环境变量中的 github 访问令牌发出一个 pull 请求。

好吧！我们已经分解了 bash 脚本。记住——只需更改基本分支、创建的分支和文件创建部分的名称，以便在您自己的项目中使用该脚本。在您的本地环境中运行这个脚本——假设您设置了您的环境 GITHUB _ ACCESS _ TOKEN var——应该可以顺利运行，并在 GITHUB 上创建一个 pull 请求。

接下来，我们来编辑一下我们的圈 CI。只不过是给你的正常圈子加了几行代码而已. yml:

```
# Other Circle.yml code

test:
  post:
    - >
      if [ -n "${RUN_NIGHTLY_BUILD}" ]; then
        # If you put the script in your scripts folder, the path is ./scripts/<name>
        ./bundle_update.sh
        # As many scripts as you want here
      fi

# More circle CI code 
```

Enter fullscreen mode Exit fullscreen mode

这段代码说“如果我们得到一个 post 请求，它有一个名为 RUN_NIGHTLY_BUILD 的变量，执行下面的脚本”。

就是这样！将此代码推送到您想要调用它的分支。例如，您可能希望将所有脚本保存在 builds 分支中。只需将上面的脚本和 yml 文件合并到一个名为 builds 的分支中，并推送该分支。您已经准备好触发您的构建了！您也可以使用一个 staging 分支，develop 分支，等等。

超级重要的注意事项:如果您有多个脚本集要运行，并且您只将脚本存储在一个分支中——比如说，`builds`——并且您在一个脚本中更改了分支，那么您必须在脚本运行之间/在每个脚本结束时运行`git checkout builds`。如果不这样做，Circle CI 可能无法运行您的所有脚本，因为您将更改到一个它们不存在的分支！

好的，接下来让我们设置我们的 lambda 并完成它。

首先，跳到 Circle CI，转到项目设置，然后转到 API 权限，然后单击创建令牌。令牌的范围应该是`All`，标签可以是你想要的任何东西。我选了`Lambda Execution`。

保存令牌，并打开 AWS 控制面板。切换到λ。

单击创建 Lambda 函数，然后单击空白函数。接下来，单击单词 Lambda 左侧的空虚线框，然后单击 Cloudwatch Events 作为触发器。对于规则，创建新规则。规则名为`Nightly-Build`，计划表达式为`cron(0 0 * * ? *)`-每天在 12 UTC 运行。[(来源)](http://docs.aws.amazon.com/lambda/latest/dg/tutorial-scheduled-events-schedule-expressions.html)

选中启用触发器，然后移至下一屏幕。

将函数命名为‘nightly build’。现在跳过代码。

将环境变量 CircleCIToken 设置为。

将角色设置为基本 lambda 执行角色。

在高级设置下，将超时设置为 15 秒。

最后，[的λ码](https://github.com/Rob117/CircleCI-Nightly-Builds/blob/master/lambda.js) :

```
// Based on: https://github.com/godfreyhobbs/circleci-cron-serverless/blob/master/handler.js
'use strict';
var http = require("https");

exports.handler = (event, context, callback) => {
    var options = {
        "method": "POST",
        "hostname": "circleci.com",
        "port": null,
        // example: "/api/v1.1/project/github/RobUserName117/secret-project/tree/builds"
        "path": "/api/v1.1/project/<github or bitbucket>/<organization or user name>/<project name>/tree/<branch name with scripts>" +
        "?circle-token=" + process.env.CircleCIToken,
        "headers": {
            "content-type": "application/json",
            "cache-control": "no-cache"
        }
    };

    // Make request, write out all of response
    var req = http.request(options, function (res) {
        var chunks = [];

        res.on("data", function (chunk) {
            chunks.push(chunk);
        });

        res.on("end", function () {
            var body = Buffer.concat(chunks);
            const response = body.toString();
            console.log(response);
            callback(null, response);
        });
    });

    req.write(JSON.stringify({
      "build_parameters": {
        "RUN_NIGHTLY_BUILD": "true"
      }
    }));
    req.end();
}; 
```

Enter fullscreen mode Exit fullscreen mode

这段代码的大部分是自我解释的。我想让您注意的一行是 options 对象的“path”部分。根据[圈 CI 文件](https://circleci.com/docs/1.0/nightly-builds/)，你发布到的网址是`/api/v1.1/project/vcs system/organization or user name/project name/tree/branch`

因此，如果您想推送到存储在 github 中的 Google 组织的 blogly 项目的 staging 分支，您可以使用:
`/api/v1.1/project/github/google/blogly/tree/staging`

第二件事是我们通过发送一个将 RUN_NIGHTLY_BUILD 参数设置为 true 的 JSON 来调用我们的构建。你可以设置或发送尽可能多的变量——详见文档。

当这段代码成功运行时(单击“TEST”)，您应该会得到类似“最后一次构建是等等等等”的响应。如果您看到一个类似 CircleCI 网页 HTML 的响应，那么您在某个地方出现了配置错误。

切换到 CircleCI，看着你的构建发生，然后切换到 Github，见证你光荣的拉请求！

完了！感谢阅读到最后。如果您有任何问题，请直接给我发消息或留下评论。