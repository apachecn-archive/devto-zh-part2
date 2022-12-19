# 在重写应用程序时学习科尔多瓦语

> 原文：<https://dev.to/huijing/learning-cordova-while-rewriting-an-app-3i7e>

这是一篇“重构遗留代码”的帖子，我怀疑在接下来的几个月里你会看到更多这样的帖子，因为我已经决定通过从事一些有报酬的工作来做一些成人化的事情。该角色的主要部分包括现有前端代码库的看门人职责。

我满怀爱意地说*清洁工*。在我看来，有两种开发人员，一种喜欢创造酷的东西，像人们换内衣一样发布新的应用程序。还有一些人在人群已经回家，灯已经关掉后进来，将代码重构为可靠的东西，编写文档，基本上是在聚会后进行清扫。

我是第二类开发者。看，谁不喜欢玩新东西呢？我们大多数人可能都是这样。也许吧。但是我在重构和清理工作中找到了真正的安慰。因为我是个奇怪的人。如果你见到我本人，请记住这一点。

## 嘿，所以我们有这个演示…

我目前的公司从事欺诈检测系统业务，即支付欺诈。我们可以通过我们的产品在多个领域提供服务，其中之一就是为银行和金融机构提供客户服务。

总之，长话短说，业务团队已经开发了一个非常有趣的概念，技术团队中的某个人已经构建了一个概念验证演示来展示上述概念。

这是一个由两部分组成的项目，这意味着设置包括一个在平板电脑上运行的应用程序和一个在线仪表盘。我们的销售团队在接触潜在客户时会不时地使用该演示，老实说，当我第一次看到它时，我也留下了深刻的印象。因为这个概念是有意义的，并且演示说明了流程，使得解释更加有说服力。

但是，作为一个轻度强迫症患者，有些事情突然出现在我眼前，比如，我不知道，网页的标题是“免费引导管理模板:梦想”。为了提供一些我所处情况的背景，这个团队以前从来没有真正的前端开发人员。据我所知，团队一直专注于构建公司核心产品的后端功能。

我一点也不惊讶所有的东西都是用 Bootstrap 构建的。但这就是我现在有工作的原因，对吗？此外，从演示制作到现在，公司经历了一次公司品牌重塑，所以我们现在有了不同的公司颜色。这是更新演示的最佳时机。

## 原生 app？但是我太洋气了…

抱歉，那是一个可怕的玩笑。但说实话，我这辈子从来没做过一个原生应用。然而，在进一步检查代码库后，我意识到这并不是一个真正的本地应用程序，它是用 [Cordova](https://cordova.apache.org/) 构建的。这个我可以处理。

我花在为 Cordova 开发排除本地环境故障上的时间是荒谬的。我最终安装了 [Android Studio](https://developer.android.com/studio/index.html) (虽然我实际上并不使用它做任何事情)，但是从我的终端用 [Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/#installing-the-cordova-cli) 运行一切。

我也花了两天时间才意识到，与其试图修复不同工具之间的所有版本兼容性问题，不如跟踪原始开发人员使用的工具的版本并使用它们。基于存储库中的提交日期，我降级到 [Cordova 6.5.0](https://cordova.apache.org/news/2017/01/23/tools-release.html) ，一切都神奇地工作了。我还通过[自制软件](https://brew.sh/)安装了 [Gradle](https://gradle.org/) ，因为 Android Studio 版本就是不行。

关于这个遗留演示的另一件事是存储库没有`.gitignore`文件。所以所有的构建工件碰巧也在存储库中结束了。由于我在两天的设置过程中被卡住了，所以我通读了 Cordova 文档并看到了关于[版本控制](https://cordova.apache.org/docs/en/latest/reference/cordova-cli/#version-control)的这一部分，其中写道:

> 建议不要将`platforms/`和`plugins/`目录签入版本控制，因为它们被认为是构建工件。您的平台和插件将自动保存在`config.xml` & `package.json`中。当`cordova prepare`被调用时，这些平台/插件将被下载到机器上。

在我职业生涯的早期，我有过[炸毁 Git 库](https://www.chenhuijing.com/blog/the-epic-git-bomb/)的不幸经历，从那以后，我总是确保我的库已经正确地建立了`.gitignore`文件。因为，清理一个被炸的仓库一点都不好玩。我的`.gitignore`文件最终看起来是这样的:

```
# Mac
.DS_Store
.AppleDouble
.LSOverride
.DocumentRevisions-V100
.fseventsd
.Spotlight-V100
.TemporaryItems
.Trashes
.VolumeIcon.icns
.com.apple.timemachine.donotpresent
.AppleDB
.AppleDesktop
.apdisk
.idea

# Windows
ehthumbs.db
ehthumbs_vista.db
*.stackdump
[Dd]esktop.ini
$RECYCLE.BIN/
*.lnk

# Build artifacts
capture_plus/platforms
capture_plus/plugins
node_modules

# IDE files
android.iml
CordovaLib.iml 
```

Enter fullscreen mode Exit fullscreen mode

我还创建了一个新的存储库，因为原来的存储库中有太多我不喜欢的历史。我想这就像是逃避，但我现在时间紧迫，我的朋友们。生活并不完美，我也不会做出完美的决定。

## 这是什么科尔多瓦？

根据[文档](https://cordova.apache.org/docs/en/latest/guide/overview/index.html)，Apache Cordova 是一个开源移动开发框架，它允许我们使用标准的 web 技术——HTML、CSS 和 Javascript——进行跨平台开发。

这使我有可能像对待一个典型的 web 应用程序一样对待这个项目，并为 Sass 编译、Babel transpiling 和 Nunjucks 设置我的常规 gulp 工作流作为模板语言。

我的项目文件夹结构最终看起来是这样的:

```
PROJECT_NAME/
|-- .git/
|-- .gitignore
`-- APP_FOLDER/
    |-- config.xml
    |-- hooks/
    |-- platforms/
    |-- res/
    `-- www/
|-- gulpfile.js
|-- node_modules/
|-- package.json
|-- README.md
`-- src/
    |-- js/
    |-- pages/
    |-- scss/
    `-- templates/ 
```

Enter fullscreen mode Exit fullscreen mode

`APP_FOLDER`是运行所有 Cordova 相关命令的地方，只包含与应用程序相关的文件。我的源代码都放在`src`中，gulp 会将它们处理到`www`文件夹中。我们的大多数项目都是用 Python 构建的，所以 [Flask](http://flask.pocoo.org/) 是开发基于 web 的 UI 部分的常用框架。

Flask 使用 [Jinja2](http://jinja.pocoo.org/docs/2.10/) 作为它的模板语言，而 [Nunjucks](https://mozilla.github.io/nunjucks/) 使用非常相似的语法，因为它一开始就是基于 Jinja2 的。模板语言确实简化了开发工作流程，因为它具有使用包含和宏、模板继承、使用迭代器等特性。

## 囿于科尔多瓦的发展

如前所述，我的工作流程包括三个主要任务，将 Sass 编译成 CSS，用 Babel 传输 ES6，将 Nunjucks 模板编译成 HTML。基于上面概述的文件夹结构，以下是三个任务的吞咽功能:

```
gulp.task('sass', function () {
  return gulp.src('src/scss/styles.scss')
  .pipe(sass({
    includePaths: ['scss'],
    onError: browserSync.notify
  }))
  .pipe(prefix(['last 3 versions', '> 3%'], { cascade: true }))
  .pipe(gulp.dest('capture_plus/www/css'))
  .pipe(browserSync.reload({stream:true}))
});

gulp.task('scripts', function() {
  return gulp.src(['src/js/*.js'])
  .pipe(babel({
    presets: ['env']
  }))
  .pipe(gulp.dest('capture_plus/www/js'))
  .pipe(browserSync.reload({stream:true}))
});

gulp.task('nunjucks', function() {
  return gulp.src('src/pages/**/*.+(njk)')
  .pipe(render({
      path: ['src/templates']
    }))
  .pipe(gulp.dest('capture_plus/www'))
  .pipe(browserSync.reload({stream:true}))
}); 
```

Enter fullscreen mode Exit fullscreen mode

我工作流程的一个关键部分是使用[浏览器同步](https://browsersync.io/)。因此，它也进入了`gulpfile.js`:

```
gulp.task('browser-sync', ['nunjucks', 'sass', 'scripts'], function() {
  browserSync.init({
    server: "APP_NAME/www",
    port: 6001 /* Pick your favourite port number */
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 在设备上调试

由于这个演示将部署到我们公司的平板电脑上，这些平板电脑都是三星 Galaxy S2s，所以我只关注 Android 部分。如果我们在 iOS 设备上演示，我会告诉你的。我喜欢 Android 平板电脑的一点是，一旦你通过 USB 将其连接到你的电脑，你就可以在使用 Chrome 时像在桌面浏览器上一样查看东西。

要做到这一点，你必须打开开发者模式，这包括找到你的设备的*构建号*，通常在*设置*中的*关于设备*部分下。点击*构建号*七次将解锁*开发者模式*。你会知道你在正确的轨道上，因为在轻击几下后会弹出一个通知，说“你现在离成为开发者还有 X 步之遥。”。

一旦发生这种情况，将有一个新的部分称为*开发者选项*，在那里你可以打开 USB 调试。这使得我们可以通过在地址栏中输入`chrome://inspect`来访问 Chrome Webview 调试工具。

<figcaption>Chrome 可以判断你是否连接了安卓设备</figcaption>

[![Chrome webview debugging tool](../Images/e6d2f6d8c887cd66f6534ceefe886793.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lK4ebemp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/assets/images/posts/cordova/webview-debug-640.jpg)

要在 Android 设备上测试和调试你的 Cordova 应用程序，你需要使用这个命令，这里的`-debug`标志很重要:

```
cordova run android -debug --device 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>像检查网站一样检查你的应用</figcaption>

[![Developer tools for your app](../Images/da4f0e4197f08784e91bb5aed07dc22c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S-0Sas80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/assets/images/posts/cordova/debug-cordova-640.jpg)

唯一的小问题是，每次对代码进行更改时，都必须重新部署到设备上进行测试。但是对于我正在构建的这个应用程序，我唯一一次必须快速连续地这样做是在我使用设备 API 的时候。其余的时间我只是在电脑上工作，因为 Browysersync 是按照普通网站提供文件的。

## 使用 Cordova 插件访问设备 API

作为第一次使用科尔多瓦的人，我没有意识到[插件生态系统](https://cordova.apache.org/plugins/)已经处理了多少繁重的工作，直到我深入研究了使用[科尔多瓦插件相机预览](https://github.com/cordova-plugin-camera-preview/cordova-plugin-camera-preview)和用于科尔多瓦的 [card.io 插件](https://github.com/card-io/card.io-Cordova-Plugin)的代码部分。

这两个插件的文档非常好，我并没有花太多的时间去弄清楚如何让它们工作。演示的一部分包括在注册过程中给用户拍照。相机预览的`takePicture`功能提供了设置宽度、高度和质量的选项，并将图像数据作为 base64 编码的 jpeg 图像返回，然后您可以根据需要进行处理。

显示图像包括添加一些 CSS 样式，以确保事情排列得很好，特别是`object-fit`。这个属性在我最喜欢的 CSS 属性列表中慢慢攀升。

```
.selfie-image {
  width: 400px;
  height: 400px;
  object-fit: cover;
  object-position: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

该应用程序的另一个有趣部分是，你可以拍摄政府颁发的身份证照片，应用程序可以提取相关细节，如卡号和发行国家。为了方便我们自己，我们只“接受”马来西亚和新加坡的卡。

为此，我们使用了 [Google Vision API](https://cloud.google.com/vision/) 进行文本提取，它实际上非常好。我想卡片本身印刷清晰，格式标准，所以也许谷歌提取文字并不难。基于处理后的图像，我们可以在 UI 中显示提取的文本，而不是让用户手动输入。

card.io 是由 PayPal 的人开发的，在移动应用程序中提供简单的信用卡扫描。我们的演示也有一个信用卡扫描组件。该库附带了一组函数，这些函数从信用卡扫描中返回相关字段，因此您可以根据需要使用它们。如果由于某种原因扫描失败，总是有通过设备键盘手动输入的选项。

我从这个练习中学到的一件事是，信用卡的前六位数字标识了该卡的品牌、发行该卡的金融机构以及发行国家。基本的信用卡号码验证依赖于 [Luhn 算法](https://planetcalc.com/2464/)，这是一个简单的校验和公式，如果校验和 mod 10 等于零，则认为号码序列有效。

很高兴知道您是否需要生成信用卡号码来进行测试。像我一样。

## 删除 jQuery 是一种宣泄

一旦我建立了自己的工作流，我就可以编写 ES6 语法，而不用太担心浏览器支持。为了安全起见，我确实为[承诺](https://github.com/taylorhakes/promise-polyfill)和[获取](https://github.com/github/fetch)包含了聚合填充。最初实现中的所有 jQuery 都在 ES6 中重写，并尽可能地进行了简化(假设有一些函数是不必要的，只是一些)。

这是我熟悉 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 的好方法，因为我必须转换所有的`$.ajax()` `POST`请求来使用 Fetch。大部分是关于`cors`的问题，但是用谷歌搜索正确的设置并不难。

例如，对照我们的 [BIN 数据库](http://binbase.net/)检查卡号的`POST`请求看起来像这样:

```
var url = encodeURI("http://SOME_SERVER_ADDRESS.com/bank/app?binbase="+binbase+"&lastfour="+lastfour+"&id="+id+"&amount="+amount)

var settings = {
  "async": true,
  "crossDomain": true,
  "url": url,
  "method": "POST",
  "dataType": "json",
  "headers": {
    "cache-control": "no-cache",
  },
  "complete": function(){
      window.location.href="sms_verification.html"
  },
}

$.ajax(settings).done(function (response) {
  // console.log(response)
  if (response.status=="ok"){
    console.log("success")
  }else if(response.status=="fail"){
    console.log("fail")
  }else{
    console.log("error")
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

重构后的版本如下所示:

```
const url = encodeURI(uiServerUrl + '/bank/app?binbase=' + binCheck + '&lastfour=' + lastfour + '&id=' + userId + '&amount='+ verificationAmount);
fetch(url, {
  method: 'POST',
  mode: 'cors',
  headers: new Headers({
    'Content-Type': 'application/json'
  })
})
.then(checkStatus).then(function(response) {
  return response.json();
}).then(function(data) {
  console.log('Bin check status: ' + data.status);
  window.location.href = 'verification.html';
}).catch(function(error) {
  console.log('request failed', error)
}) 
```

Enter fullscreen mode Exit fullscreen mode

我的方法更好吗？我真的说不准，因为两种实现达到了相同的结果，但是我真的更喜欢用 Fetch，你知道吗？

## 包装完毕

这只是我第一次使用 Cordova 的概述，我认为这是一个非常有用的概念验证框架，尤其是当你手头没有任何本地开发人员的时候。我会将它用于一个成熟的本地应用吗？在这一点上，我想说可能没有，但我知道什么？

这个演示的另一部分我很满意，但它与科尔多瓦无关，所以这将出现在另一个帖子中。这一部分包括主题切换与萨斯地图，和各地的乐趣。朋友们，敬请关注。