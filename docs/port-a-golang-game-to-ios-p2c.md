# 将 Golang 游戏移植到 iOS

> 原文：<https://dev.to/ntoooop/port-a-golang-game-to-ios-p2c>

这些天，我正在把我们的安卓游戏[射击堆栈](https://play.google.com/store/apps/details?id=io.korok.shootingblock)移植到 iOS 上。是用 Golang 写的游戏，用我们自己的[游戏引擎](https://korok.io)。本文将提供一些将 Golang 游戏移植到 iOS 的技巧和经验。如果你曾经使用过 GoMobile，你就会知道`gomobile build`可以构建`.app`并直接安装到 iOS 设备上。但如果你想整合第三方 SDK，比如广告、分析，用`gomobile build`是不可能的。我们需要一种方法来构建库，然后我们可以在 xcode 和`gomobile bind`中使用它。

下面是我们的项目结构:

```
✗ tree .
.
├── arena.go
├── audio.go
├── build.md
├── color.go
├── file.go
├── flash_message.go
├── gameover.go
├── input_glfw.go
├── input_mob.go
├── main.go
├── particle.go
├── ready.go
└── welcome.go 
```

Enter fullscreen mode Exit fullscreen mode

是的，都在一个主包里。使用`gomobile bind`命令，我们可以为 xcode 构建`.framework`文件。但是我们仍然需要一种方法来调用 lib 的`main`方法。所有 xcode 项目都有一个`main.h`文件:

```
int main(int argc, char * argv[]) {
@autoreleasepool {
    return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
}
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以在这里调用 lib 的 main 方法。就像我上面说的，我所有的代码都在一个`main`包里，但是 GoMobile 不能在`main`里绑定文件(就是不支持)。所以我必须对我的项目做一点改变:

```
✗ tree .
.
├── ios
│   └── ios.go
└── main
    ├── game
    │   ├── arena.go
    │   ├── audio.go
    │   ├── build.md
    │   ├── color.go
    │   ├── file.go
    │   ├── flash_message.go
    │   ├── gameover.go
    │   ├── input_glfw.go
    │   ├── input_mob.go
    │   ├── main.go
    │   ├── particle.go
    │   ├── ready.go
    │   └── welcome.go
    └── main.go 
```

Enter fullscreen mode Exit fullscreen mode

我把源代码放在`main/game`包里，那么我还是可以用`gomobile build`来构建桌面和 Android。然后我创建了`ios/ios.go`文件，它不是主文件，所以我可以用`gomobile bind`来创建它，内容:

```
package ios

import (
   "korok.io/korok/gfx/dbg"
   "korok.io/korok"
   "korok.io/korok/math/f32"
   "main/game"
)

func Run() {
   dbg.DEBUG = dbg.None
   option := korok.Options{
      Width:360,
      Height:640,
      Clear:f32.Vec4{0,0,0,1},
   }
   korok.Run(&option, &game.StartScene{})
} 
```

Enter fullscreen mode Exit fullscreen mode

方法`Run`与`main.main()`相同:

```
func main() {
    dbg.DEBUG = dbg.None
    option := korok.Options{
        Width:360,
        Height:640,
        Clear:f32.Vec4{0,0,0,1},
    }
    korok.Run(&option, &StartScene{})
} 
```

Enter fullscreen mode Exit fullscreen mode

创建它只是为了简化绑定过程。现在，我们可以使用`gomobile bind -target=ios ios`来构建一个`.framework`。如果一切正常，接下来，我们可以将`.framework`集成到我们的 xcode 项目中(只需将它拖到 xcode 中)。修改`main.h`文件如下:

```
#import "Ios/Ios.h"

int main(int argc, char * argv[]) {
    IosRun();
} 
```

Enter fullscreen mode Exit fullscreen mode

构建&运行，现在项目(进入内部)工作了。

## 如何添加 ads SDK？

现在，我们已经成功创建了一个库，并在 xcode 项目中使用了它。如果我们想集成 Ad SDK，我们仍然需要在 GoMobile 使用的原始`.m`文件中添加一些生命周期挂钩。在`x/mobile/app`包中，我们可以找到一个文件`darwin_ios.m`，这是 GoMobile 用来构建 iOS 应用的文件，这里定义了两个`@interface`:

```
@interface GoAppAppController : GLKViewController<UIContentContainer, GLKViewDelegate>
@end

@interface GoAppAppDelegate : UIResponder<UIApplicationDelegate>
@end 
```

Enter fullscreen mode Exit fullscreen mode

我将挂钩生命周期方法，以便在 xcode 项目中使用:

```
@implementation GoAppAppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    ...
    [self kkDidFinishLaunching];
    return YES;
}
- (void)viewDidLoad {
    ...
    [self kkViewDidLoad];
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在 xcode 项目中创建一个文件(`ad.m`)，添加以下代码:

```
@implementation GoAppAppDelegate(AD)
-(void)kkDidFinishLaunching {
    NSLog(@"hi, implemenmt method");
}
@end

@interface GoAppAppController(AD)
@end

@implementation GoAppAppController(AD)

- (void)kkViewDidLoad {
    NSLog(@"view did load2..");
@end 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用了 Obj-C 语言中的类别。它将实现我们在“darwin_ios.h”中使用的方法。

构建和运行，您应该看到控制台中打印的日志信息。

这是我们游戏的快照(有广告，但我会在发布时删除):

[![Golang game with admob](img/482a90f68c5e1e1e6b988f1413e9ef15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9E-Usm5K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vgvwlr5gxjwac1o8xwek.jpg)

## 其他问题

生活并不容易，事实上，构建一个实际的游戏并在我的 iPhone6 上运行它需要几天时间。有一个问题，我只是不知道如何解决它，问题是当应用程序暂停和反应到前台的应用程序将冻结。我已经测试了 GoMobile 提供的基本例子，它也有问题，它的错误。在基本的例子中，它使用`switch e.Crosses(lifecycle. StageVisible)`来管理应用程序的生命周期，如果你将`lifecycle. StageVisible`改为`lifecycle. StageAlive`，它就会冻结。

几天来我一直在钻研这个问题。看起来 GoMobile 使用了一个自定义循环来绘制 GL 命令，并调用:

```
[EAGLContext setCurrentContext:ctx];
[ctx presentRenderbuffer:GL_RENDERBUFFER]; 
```

Enter fullscreen mode Exit fullscreen mode

来交换缓冲区，但如果应用程序挂起并再次激活，此代码将失败。我尝试了几种方法来解决这个问题，但并不像我想的那么简单，最后，我放弃了，并在 Github 中创建了一个问题:[在 iOS](https://github.com/golang/go/issues/27483) 上从暂停状态恢复后应用程序冻结。但我仍然需要一种方法来解决这个 bug，在 git commit 中做了一些研究后，我发现旧的实现——只是使用 system 循环而不是 Go 循环。所以我回滚到旧的实现:

```
self.glview.enableSetNeedsDisplay = NO;
CADisplayLink* displayLink = [CADisplayLink displayLinkWithTarget:self selector:@selector(render:)];
[displayLink addToRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];

- (void)render:(CADisplayLink*)displayLink {
    [self.glview display];
}

- (void)glkView:(GLKView *)view drawInRect:(CGRect)rect {
    drawloop();
}
//export drawloop
func drawloop() {
    runtime.LockOSThread()
    defer runtime.UnlockOSThread()

    for workAvailable := theApp.worker.WorkAvailable();;{
        select {
        case <-workAvailable:
            theApp.worker.DoWork()
        case <-theApp.publish:
            theApp.publishResult <- PublishResult{}
            return
        case <-time.After(50 * time.Millisecond): // incase the method blocked!!
            return
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我还在 Cocos2DX 和 [Ebiten](https://github.com/hajimehoshi/ebiten) 中发现了相同的(大部分)实现。最后，所有的问题都解决了！！我正在将游戏上传到 Apple Store Connect，你可以在几天或几周内下载(你知道 AppStore 审核也不容易)。