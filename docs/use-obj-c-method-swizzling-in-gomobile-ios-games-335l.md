# 在 GoMobile iOS 游戏中使用 obj-c 方法 swizzling

> 原文：<https://dev.to/ntoooop/use-obj-c-method-swizzling-in-gomobile-ios-games-335l>

我们已经使用 gomobile 开发了 Android 和 iOS 的游戏。但要增加像整合广告/分析这样的新功能并不容易。在 android 上，我们只需解包用 gomobile 构建的 Apk，并在新的 Android 项目中直接使用`.so`。但在 iOS 上，这是不可能的。几周前我写了一篇关于如何整合 ads SDK 的文章，你可以在这里找到它。在文章中，我修改了 gomobile 的源代码，并用两个钩子方法打了补丁。这些天，我发现了一个新的方法，不用修改源代码，它的神奇之处在于 Object-C，叫做*方法 Swizzling* 。

方法切换提供了一种交换方法实现的方式，所以，你可以用它来给一个现有的类添加新的特性。我们是这样做的:

```
@implementation GoAppAppController(AD)

+ (void)load {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        Class class = [self class];

        SEL originalSelector = @selector(viewDidLoad);
        SEL swizzledSelector = @selector(kkViewDidLoad);

        Method originalMethod = class_getInstanceMethod(class, originalSelector);
        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);

        BOOL success = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));
        if (!success) {
            method_exchangeImplementations(originalMethod, swizzledMethod);
        }
    });
}

- (void)kkViewDidLoad {
    [self kkViewDidLoad];

    // Initialize Google Mobile Ads SDK
    [GADMobileAds configureWithApplicationID:@"..."];
} 
```

Enter fullscreen mode Exit fullscreen mode

在`+ (void)load`方法中，我使用方法 swizzling 来交换`GoAppAppController`的 viewDidLoad 方法和我的`kkViewDidLoad`方法的实现。`GoAppAppController`是 gomobile 对 UIViewController 的实现，你可以在`x/mobile/app/darwin_ios.m`找到源代码。在`kkViewDidLoad`方法中，我调用`[self kkViewDidLoad]`来调用原来的`viewDidLoad`，然后，调用`GADMobileAds`来初始化我的 Admob Ads SDK。

就这些，很简洁也很容易！我还在我的第二个游戏*不稳定塔*中使用了它，但是，不幸的是，苹果公司说它与 AppStore 中现有的游戏相似，所以它没有通过审查。还有一个安卓版本，如果你喜欢，可以在 [GooglePlay](https://play.google.com/store/apps/details?id=io.korok.unstabletower) 下载。