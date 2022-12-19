# 让我们建造查克·诺里斯！-第 8 部分:在 iOS 应用程序中使用 C++

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-8-using-c-in-an-ios-application-3pgi>

*最初发表于[我的博客](https://dmerej.info/blog/post/chuck-norris-part-8-ios/)。*

*注意:这是[的第 8 部分，让我们打造查克·诺里斯！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

如果您在阅读完第 1 到第 7 部分后已经走了这么远，那么恭喜您！

在这篇文章中，我们将使用到目前为止所学的一切，编写一个能够展示 Chuck Norris 事实的 iOS 应用程序。我们还将学习一些 iOS 和 Xcode 特有的东西。让我们开始吧！

# 简介:椰子

有许多方法可以实现我们的目标。有几种工具，但是在这篇文章中，我们将集中讨论 [CocoaPods](https://cocoapods.org) 。 <sup id="fnref1">[1](#fn1)</sup>

CocoaPods 可用于两种模式:

首先，你可以运行`pod lib create foo`。这将创建一个`Foo.podspec`文件。 *podspec* 描述了如何构建`foo`库，有点像柯南的菜谱。然后，您可以将 podspec 文件和相关的源代码上传到一个*存储库*。

第二，可以在现有 Xcode 项目旁边运行`pod init`。这将创建一个 Xcode *工作区*和一个`Podfile`文件。您可以在 Podfile 中指定一个或多个依赖项。然后，当您运行`pod install`时，将获取 podspecs 并配置工作空间，以便您可以在原始 Xcode 项目中构建和使用这些依赖项。

这样，您可以通过 Xcode 的 GUI 最大限度地减少与 Xcode 的交互，而是使用代码。

计划是这样的:

*   在`ios/bindings/ChuckNorrisBindings`中创建一个名为`ChuckNorrisBindings`的 *CocoaPods 库*。
*   在`ios/app/ChuckNorris`目录中创建一个名为 ChuckNorris 的空白 iOS 应用程序。
*   使用 CocoaPods 创建 ChuckNorrisBindings 和 ChuckNorris 之间的依赖关系。
*   …
*   利润！

# 绑定

我们运行`pod lib create ChuckNorrisBindings`并回答几个问题:

```
$ pod lib create ChuckNorrisBindings
Cloning `https://github.com/CocoaPods/pod-template.git` into `ChuckNorrisBindings`.
...
What platform do you want to use? [iOS / macOS]
> ios
What language do you want to use? [Swift / ObjC]
> ObjC
Would you like to include a demo application with your library? [Yes / No]
> No
Which testing frameworks will you use? [Specta / Kiwi / None]
> Specta
Would you like to do view based testing? [Yes / No]
> No
What is your class prefix?
CK
...
Running pod install on your new library.
...
Analyzing dependencies
Fetching podspec for `ChuckNorrisBindings` from `../`
Setting up CocoaPods master repo
...
 Ace! you're ready to go!
 We will start you off by opening your project in Xcode
  open 'ChuckNorrisBindings/Example/ChuckNorrisBindings.xcworkspace' 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到 CocoaPods 创建了很多文件。其中包括:

*   `ChuckNorrisBindings/Sources`:图书馆的来源。
*   `ChuckNorrisBindings.podspec`:用于构建和依赖 ChuckNorrisBindings 库的 podspec
*   `Example/ChuckNorrisBindings.xcodeproj`Xcode 项目构建源码
*   `Example/ChuckNorrisBindings.xcworkspace`Xcode 工作区，我们可以使用它来:
    *   建造图书馆
    *   构建测试框架
    *   构建并运行测试本身。

如果我们试图直接从 Xcode 运行测试，它不会马上工作，相反我们会得到这个错误消息:

```
Scheme "Pods-ChuckNorrisBindings_Tests" is not configured for testing. 
Edit the scheme to enable testing, or cancel the action. 
```

Enter fullscreen mode Exit fullscreen mode

我们不得不篡改 CocoaPods 为我们制定的计划:

打开方案编辑器:

[![edit the testing scheme](../Images/a7d3ff2df97d3409a78aa455d4ff4a5b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JhPA1KQB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/manage-schemes.png)

显示测试方案:

[![show test scheme](../Images/4b8c375628fa1d1a3b98c482ed8635f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6O6jt1kA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/show-test-scheme.png)

点击底部的`+`按钮，选择测试目标。

[![choose the test target](../Images/81a0ab9aa4cef8cf628af5b4eaaca14e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NJrgiTMB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/choose-test-target.png)

这一次我们得到了另一个错误消息:

```
Could not find test host for ChuckNorrisBindings_Tests ... 
```

Enter fullscreen mode Exit fullscreen mode

在“常规”下编辑项目设置，并将宿主应用程序表单“自定义”切换到“无”:

[![select test host](../Images/8b0ea1a39b1cbcaffdb4422aedcb9d2f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iuSQutek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/select-test-host-app.png)

它起作用了:模拟器启动，测试运行。

### 为 iOS 交叉编译 ChuckNorris

我们为 iOS 绑定 C++库的计划是我们已经在 *[第 5 部分:Python 与 cffi](https://dmerej.info/blog/post/chuck-norris-part-5-python-cffi/)* 和 *[第 6 部分:Android](https://dmerej.info/blog/post/chuck-norris-part-6-android-cross-compilation/)* 交叉编译中看到的技术的组合。我们将把 ChuckNorris 作为静态库从 macOS 交叉编译到 iOS。然后我们将编译 Objective-C 代码，给它提供到`libchucknorris.a`文件、柯南依赖项和`chucknorris.h` C 头文件的路径。

首先，**让我们通过运行`xcode-select --install`来确保 Xcode 命令行工具已经安装**。如果你在家学习这个教程，确保运行它，否则什么都不会起作用。

让我们创建一个名为`ios`的柯南概要文件，并添加一个构建依赖项:

```
# In ~/.conan/profiles/ios [settings]
os=iOS
os.version=9.0
arch=x86_64
compiler=apple-clang
compiler.version=9.0
compiler.libcxx=libc++
build_type=Release
os_build=Macos

[build_requires]
darwin-toolchain/1.0@theodelrieu/stable 
```

Enter fullscreen mode Exit fullscreen mode

目前，我们已经对 x86_64 架构进行了硬编码，因为我们将在模拟器中运行一切，而 Xcode 模拟器需要这种架构。为了在实际设备上运行代码，我们将使用正确的`--settings arch=<arch>`标志调用 conan。这类似于我们在第 6 部分中所做的。

注意，这次我们不需要自己写 toolchain 配方了，柯南中心上已经有一个了。 <sup id="fnref2">[2](#fn2)</sup>

然后，正如我们对 Android 所做的那样，我们对`sqlite3`运行`conan create`。

```
cd conan/sqlite3
conan create . dmerej/test --profile ios --settings arch=x86_64 
```

Enter fullscreen mode Exit fullscreen mode

正如我们对 Android 的`libc++shared.so`文件所做的那样，我们修补了 ChuckNorris 方法来处理所有`.a`文件的副本，包括在`imports()`和`package()`方法:
中

```
def imports():
      if self.settings.os == "Android":
          self.copy("*libc++_shared.so", dst="lib", keep_path=False)
      if self.settings.os == "iOS":
          self.copy("*.a", dst="lib", src="lib")

def package(self):
      self.copy("bin/cpp_demo", dst="bin", keep_path=False)
      self.copy("lib/*.so", dst="lib", keep_path=False)
      self.copy("lib/*.a", dst="lib", keep_path=False) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以创建 ChuckNorris 包:

```
cd cpp/ChuckNorris
conan create . dmerej/test --profile ios --settings arch=x86_64 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利。

## pod spec

注意所有的`.a`文件都在`~/.conan/data/`里面。让我们将它们复制到绑定代码:
旁边的`nativelibs/x86_64`目录中

```
mkdir -p nativelibs/x86_64/
cp \
  ~/.conan/dataChuckNorris/0.2/dmerej/test/package/<id>/*.a
  nativelibs/x86_64/ 
```

Enter fullscreen mode Exit fullscreen mode

为了不忘记，让我们把`nativelibs`加到`.gitignore`上。

接下来，我们可以编辑 podspec 以指定:

*   包含目录:它位于一个名为`pod_target_xcconfig`的目录中。
*   *出售的*图书馆:那是我们的`libchucknorris.a`和`libsqlite3.a`。它们被称为“vendored ”,因为它们不会被 CocoaPods 本身编译，也不会出现在目标操作系统上。

```
Pod::Spec.new do |s|
  ...

  s.pod_target_xcconfig = {
    'HEADER_SEARCH_PATHS' => "${POD_ROOTS}/../../cpp/ChuckNorris/include/",
  }

  s.vendored_libraries = Dir["out/*.a"] 
```

Enter fullscreen mode Exit fullscreen mode

两个备注:

*   因为 CocoaPods 菜谱是用 Ruby 编写的，所以我们可以对`Dir`对象使用重载的`[]`操作符来获取与`out/*.a` *glob* 模式匹配的文件的完整列表。
*   `HEADER_SEARCH_PATHS`字符串包含一个`${POD_ROOTS}`扩展名，它将由 CocoaPods 设置。

请注意，无论我们构建的是`ChuckNorrisBindings` CocoaPods 库还是`ChuckNorris`应用程序，结果`HEADER_SEARCH_PATHS`都是一样的。

无论如何，我们现在可以从`Example`目录运行`pod update`来更新 Xcode 项目和工作空间配置。

# 目标-C

我们可以在 Objective-C 中直接使用 C 代码。

直接公开 C 代码仍然很危险，所以我们可以这样继续:

*   写一个 CKChuckNorris 类。(惯例是在 pod 库中的所有类前面加上项目的首字母)
*   编写一个 CKChuckNorris+Private 类别，对 CKChuckNorris 类的使用者隐藏 C 代码。

```
/* In CKChuckNorris.h */
@interface CKChuckNorris: NSObject

@property void* ckPtr;

-(instancetype)init;
-(NSString*) getFact;

@end 
```

Enter fullscreen mode Exit fullscreen mode

```
/* In CKChuckNorris.m */
#import "CKChuckNorris.h"
#import "CKChuckNorris+Private.h" 
@implementation CKChuckNorris

-(instancetype)init {
  self = [super init];
  return [self createCkPtr];
}

-(NSString *)getFact {
  return [self getFactImpl];
}

@end 
```

Enter fullscreen mode Exit fullscreen mode

```
/* In CKChuckNorris+Private.h */
#import "CKChuckNorris.h" 
@interface CKChuckNorris (Private)

-(instancetype) createCkPtr;
-(NSString*) getFactImpl;

@end 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们刚刚*声明了*的`createCKPtr`和`getFactImpl`方法。

是时候在`CKChuckNorris+Private.m`文件中*定义*了:

```
/* In CKChuckNorris+Private.m */
#import "CKChuckNorris+Private.h" 
#include "chucknorris.h" // <- Our C header file 
@implementation CKChuckNorris (Private)

-(instancetype)createCkPtr {
  self.ckPtr = chuck_norris_init();
  return self;
}

-(NSString*)getFactImpl {
  const char* fact = chuck_norris_get_fact(self.ckPtr);
  return [NSString stringWithCString:fact encoding:NSUTF8StringEncoding];
}
@end 
```

Enter fullscreen mode Exit fullscreen mode

注意依赖于`chucknorris.h` C 头文件的*唯一的*文件是`CKChuckNorris`类的*私有实现*。

有一种类似的技术叫做“PIMPL”(指向实现的指针)，你可以在 C++代码中使用它来实现同样的隔离。

现在是编辑测试的时候了:

```
/* In Tests.m */
describe(@"ChuckNorris", ^{

  it(@"can get version", ^{
    expect([CKChuckNorris versionString]).to.equal(@"0.1");
  });

  it(@"can get a fact", ^{
    CKChuckNorris* ck = [[CKChuckNorris alloc] init];
    NSString* fact = [ck getFact];
    expect(fact).toNot.beEmpty();
    NSLog(@"Got fact: %@", fact);
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

看看他们是否通过:

```
Undefined symbols for architecture x86_64:
  "operator delete(void*)", referenced from:
      _chuck_norris_version in libchucknorris.a(c_wrapper.cpp.o)
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation) 
```

Enter fullscreen mode Exit fullscreen mode

哎呀，我们忘了给 C++库添加依赖项。这比在 Android 的情况下要简单，因为他们总是为 iOS 取相同的名字:

```
Pod::Spec.new do |s|
  ...

  s.vendored_libraries = ...
  s.libraries = ['c++', 'c++abi']
end 
```

Enter fullscreen mode Exit fullscreen mode

`libc++`和`libc++abi`可以在目标操作系统上找到，因此我们使用`libraries`变量。

我们现在可以再试一次，这次测试通过了。

# GUI

我们从 Xcode 创建 Objective-C 应用程序。

[![Creating Xcode project](../Images/48e99ca880f3f0126ae4f2e4e2f12d8e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UecKekOH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/create-project.png)

然后，我们编辑主故事板，添加一个堆栈视图、一个文本字段和一个按钮:

我们将组件从 designer 视图拖放到代码中，同时按住`ctrl`键。

[![Using the assistant](../Images/770dffd06c2be9030fdab717ad57460f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yDSGD2-2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/assistant-editor.png)

现在，我们只要在点击按钮时将文本视图设置为字符串`Hello`:

```
/* In ViewController.m */
- (IBAction)onClick:(id)sender {
  self.textView.text = @"Hello";
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，这很有效。

现在我们可以运行`pod init`并编辑 Podfile:

```
target 'ChuckNorris' do
  pod 'ChuckNorrisBindings', :path => '../bindings/'
end 
```

Enter fullscreen mode Exit fullscreen mode

我们走了一条捷径。我们没有将 ChuckNorrisBindings 部署到一个规范存储库中，而是告诉 Podfile 直接从文件系统中获取 podspec。

来看看这个作品:

```
$ cd ios/app
$ pod update
...
Downloading dependencies
...
Sending stats 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错。

剩下要做的就是给控制器添加一个`CKChuckNorris*`指针，将其设置在`viewDidLoad`中，并在按钮被点击时调用`getFact()`方法:

```
/* In ViewController.h */
#import <UIKit/UIKit.h>
#import "CKChuckNorris.h" 
@interface ViewController : UIViewController

@property CKChuckNorris* ck;

@end

- (void)viewDidLoad {
  [super viewDidLoad];
  self.ck = [[CKChuckNorris alloc] init];
}

-(IBAction)onClick:(id)sender {
  self.textView.text = [self.ck getFact];
 } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以在模拟器中运行应用程序:

[![ChuckNorris application on a simulator](../Images/cbae79e05b1e8f32dcf0b71dab81b4af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pMWwkEBh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/simulator.png)

耶！

# 处理其他架构

让我们尝试构建一个`.ipa`，即可以安装在真实设备上的应用程序文件。

第一步是为我们的应用程序生成一个*归档文件*。为此，我们可以使用“产品”菜单中的“存档”条目。

现在它被禁用，但我们可以通过选择左下角播放/停止按钮旁边的“通用 iOS 设备”来解决这个问题。

我们来点一下:

```
ld: warning: ignoring file /nativelibs/x86_64/libchucknorris.a, file was built
  for archive which is not the architecture being linked (arm64)
error: Undefined symbols for architecture arm64:
  "_chuck_norris_version", referenced from:
  +[CKChuckNorris(Private) versionStringImpl] in
    libChuckNorrisBindings.a(CKChuckNorris.o) 
```

Enter fullscreen mode Exit fullscreen mode

你没看错:事实是`.a`不是为正确的 CPU 架构构建的**是一个*警告*，之后我们得到一个关于符号没有找到的一般性错误。这个故事的主旨是:当编译失败时，从编译器输出的顶部开始:)**

因此，让我们尝试添加对 Arm 位的支持。我们可以这样从交叉编译 sqlite3 开始:

```
cd sqlite3
conan create . dmerej/test --profile ios --settings arch=armv8 
```

Enter fullscreen mode Exit fullscreen mode

注意，柯南和 Xcode 对这个架构使用了不同的名称(分别是`armv8`和`arm64`)。

现在，对于 x86_64 架构，我们手动将文件从`.conan/data`复制到`nativelibs/x86_64`。这并不理想。相反，我们可以使用`conan install`(安装 deps)，然后`conan build`(构建 ChuckNorris 库)，最后`conan package`复制库和头文件(运行菜谱的`package()`函数中的代码，记得吗？):

```
$ cd cpp/ChuckNorris
$ conan install . --profile ios --settings arch=armv8 \
    --install-folder build/ios/armv8
$ conan build . --build-folder build/ios/armv8
$ connan package . --build-folder build/ios/armv8 \
    --package-folder nativelibs/armv8 
```

Enter fullscreen mode Exit fullscreen mode

注意`conan install`的“安装文件夹”与`conan build`和`conan package`的“构建文件夹”是如何匹配的。

还要注意配置文件和设置仅由`conan install`使用。在这之后，柯南需要知道的一切都可以在构建目录中找到。

让我们对“armv7”、“armv7s”和“x86”进行同样的操作。Armv7 和 armv7s 是老款 iOS 设备使用的架构，x86 是 32 位模拟器使用的架构。

由于手工操作有点繁琐，我们将使用 [build_ios.py](https://github.com/dmerejkowsky/chucknorris/blob/master/cpp/ChuckNorris/ios_build.py) 脚本。<sup id="fnref3">T33</sup>

现在，我们可以使用 *fat 库*来代替多次构建 ChuckNorrisBindings，每个架构一次(这在使用 CocoaPods 时很难做到)。Fat 库就像常规库一样，只是它们可以包含多种架构的代码。

所以让我们创建一个包含 fat `liqsqlite3.a`和 fat `libchucknorris.a`库:
的`nativelibs/universal`文件夹

```
cd nativelibs/universal
lipo -create -output libsqlite3.a ../*/lib/libsqlite3.a
lipo -create -output libchucknorris.a ../*/lib/libchucknorris.a 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用`lipo -info`检查这是否有效:

```
$ lipo -info libchucknorris.a
Architectures in the fat file: libchucknorris.a are:
  armv7 armv7s i386 x86_64 arm64 
```

Enter fullscreen mode Exit fullscreen mode

然后我们修复了 podspec:

```
- s.vendored_libraries = Dir["nativelibs/x86_64/*.a"] + s.vendored_libraries = Dir["nativelibs/universal/*.a"] 
```

Enter fullscreen mode Exit fullscreen mode

我们用`pod update --ro-repo-update`重新生成 Xcode 项目。这一次“存档”步骤没有触发任何错误，我们面对的是这样一个窗口:

[![xcode archive output](../Images/7eb08f0b5a3bb0ccbd6deec48f936045.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aEl2_wBU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/archive.png)

# 签约存档

我们现在需要从存档中构建一个可再分发的应用程序。取决于您的 Xcode 描述文件和证书。以下是对我有效的方法，你的里程数可能会有所不同:

*   在右侧窗格中选择“导出”
*   选择“临时”分发方法
*   然后勾选“从位代码重建”选项。
*   让 Xcode 进行签名，并将`.ipa`保存在基于当前日期命名的文件夹中。

最后，进入 Xcode 的“窗口”菜单，选择“设备”，然后拖放“.”。ipa”添加到该设备上安装的应用程序列表中。

下面是结果(取自我那蹩脚的安卓手机):

[![ChuckNorris running on a real device](../Images/911192178fcecf2104119474204b2d78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ez2cbwUa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/xcode/victory.jpg)

胜利！

# 结论

查克·诺里斯系列到此结束。我希望你玩得开心，我希望你学到了新的东西。下次见！

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。

* * *

1.  我听说迦太基也是个不错的选择。不过，我没有尝试过。 [↩](#fnref1)

2.  这个食谱是我来自 [tanker.io](https://tanker.io) 的好同事 Théo Delrieu 写的，并分享给大家。说谢谢！ [↩](#fnref2)

3.  顺便说一下，这与我们工作中使用的 CI 脚本相差不远。 [↩](#fnref3)