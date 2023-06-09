# 简介:浓缩咖啡测试

> 原文：<https://dev.to/apium_hub/introduction-espresso-testing-2b4b>

作为移动开发人员，我们大部分时间都在创建新的屏幕或者改变已经存在的屏幕，我们需要知道代码是否有效。这就是为什么在我们的开发过程中，我们必须使用工具来验证我们的应用程序继续按预期工作，并且我们的开发符合用户故事所期望的产品质量。在这篇文章中，我们将讨论浓缩咖啡测试。

确保:

*   当 UI 被加载时，我们向用户显示正确的信息
*   当用户执行操作时，显示所需的消息或屏幕

为了达到这个目标，有几种类型的 ui 测试:

*   经典 ui 测试->关注视图及其交互。
*   快照测试->保证完美的设计，把交互放在一边。

## 什么是 Espresso 测试？它是如何工作的？

Espresso 是 Google 为 Android 创建的一个框架，允许我们在用户界面上编写测试。它有一个简单的 API，很容易适应我们的需求，并消除了管理不同线程的复杂性。

浓缩咖啡测试基本上分为三个阶段:

*   *视图匹配器*–允许您在视图中查找项目
*   *view actions*–允许对元素执行操作
*   *视图断言*–验证视图状态

处理浓缩咖啡时，建议将这张纸放在身边。

**配置**

为了在我们的项目中进行 Espresso 测试，我们需要在我们的“app / build.gradle”文件和一个物理或虚拟设备中添加依赖项。

```
 dependencies {
 implementation fileTree(dir: 'libs', include: ['*.jar'])
 testImplementation 'junit:junit:4.12'

   // Android runner and rules support
 androidTestImplementation 'com.android.support.test🏃1.0.2'
 androidTestImplementation 'com.android.support.test:rules:1.0.2'

   // Espresso
 androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
} 
```

建议关闭我们的 android 设备的动画，我们将在开发者选项中使用这些动画进行测试。一种简单的方法是从终端执行以下命令:

```
 adb shell settings put global window_animation_scale 0.0 
adb shell settings put global transition_animation_scale 0.0 
adb shell settings put global animator_duration_scale 0.0 
```

**实施**

作为一个小的实践练习，我们已经创建了一个带有两个文本视图和一个按钮的视图，我们想要评估当我们按下按钮时，一个文本视图被隐藏，另一个被显示。

[![espresso android](img/1811f025d10f0769550cfb37244aca65.png)T2】](https://apiumhub.com/wp-content/uploads/2018/11/Screenshot-2018-11-15-at-17.19.34.png)

我们可以使用 espresso record 工具手动或自动创建我们的 ui 测试。

*   **录制浓缩咖啡**

为此，我们必须在 Android studio 的选项栏中进入**运行- >记录浓缩咖啡测试**

，它将启动一个屏幕，我们可以在那里做出断言。

[![espresso](img/68531160b2640b1ac15eb674d7c7a452.png)T2】](https://apiumhub.com/wp-content/uploads/2018/11/Screenshot-2018-11-15-at-17.29.15.png)

最后，我们将生成测试代码:

```
 @LargeTest
@RunWith(AndroidJUnit4.class)
public class RecordedTextViewToggleVisibilityTest {

  @Rule
  public ActivityTestRule mActivityTestRule = new ActivityTestRule<>(MainActivity.class);

  @Test
  public void textViewToggleVisibilityTest() {
    ViewInteraction textView = onView(
        allOf(withId(R.id.tv_hello), withText("Hello buddy!"),
            childAtPosition(
                childAtPosition(
                    withId(android.R.id.content),
                    0),
                0),
            isDisplayed()));
    textView.check(matches(withText("Hello buddy!")));

    ViewInteraction appCompatButton = onView(
        allOf(withId(R.id.button), withText("click me"),
            childAtPosition(
                childAtPosition(
                    withId(android.R.id.content),
                    0),
                2),
            isDisplayed()));
    appCompatButton.perform(click());

    ViewInteraction textView2 = onView(
        allOf(withId(R.id.tv_see_you), withText("See you"),
            childAtPosition(
                childAtPosition(
                    withId(android.R.id.content),
                    0),
                1),
            isDisplayed()));
    textView2.check(matches(isDisplayed()));
  }

  private static Matcher childAtPosition(
      final Matcher parentMatcher, final int position) {

    return new TypeSafeMatcher() {
      @Override
      public void describeTo(Description description) {
        description.appendText("Child at position " + position + " in parent ");
        parentMatcher.describeTo(description);
      }

      @Override
      public boolean matchesSafely(View view) {
        ViewParent parent = view.getParent();
        return parent instanceof ViewGroup && parentMatcher.matches(parent)
            && view.equals(((ViewGroup) parent).getChildAt(position));
      }
    };
  }
} 
```

*   **手动表单**

```
 @LargeTest
@RunWith(AndroidJUnit4.class)
public class ManualTextViewToggleVisibilityTest {

  @Rule
  public ActivityTestRule mActivityTestRule = new ActivityTestRule<>(MainActivity.class);

  @Test
  public void textViewToggleVisibilityTest() {

    // here we are going to look in the view tree
    // we use allOff from hamcrest library to combine matchers
    ViewInteraction tvHello = onView(allOf(withId(R.id.tv_hello),withText(R.String.hello_buddy)));

    ViewInteraction tvSeeYou = onView(allOf(withId(R.id.tv_see_you),withText(R.String.see_you)));

    // assert that textView with text 'Hello buddy!' is display
    tvHello.check(matches(isDisplayed()));

    // assert that textView with text 'See you' has visibility gone
tvSeeYou.check(matches(withEffectiveVisibility(ViewMatchers.Visibility.GONE)));

    // find button with given id and click
    onView(withId(R.id.button)).perform(click());

    // assert see you is visible and hello buddy is not. tvHello.check(matches(withEffectiveVisibility(ViewMatchers.Visibility.GONE)));
    tvSeeYou.check(matches(isDisplayed()));
  }
} 
```

我们可以验证 Espresso 自动生成了更多的代码，这些代码很容易被破解，需要更多的维护。例如，我们使用文字字符串来查找视图，如果更改设备的语言，测试将会失败，但是我们可以手动使用字符串资源的 id，并根据需要添加尽可能多的匹配器。

使用匹配器时，这份 [hamcrest 参考指南](https://www.marcphilipp.de/downloads/posts/2013-01-02-hamcrest-quick-reference/Hamcrest-1.3.pdf)会很有用。

**结论:浓缩咖啡测试**

作为人类，我们容易犯错误，这意味着我们的软件容易出现错误和缺陷。Espresso 帮助我们发现开发中可能出现的错误，确保客户的可靠性和他对应用程序的满意度。如果您还没有将 Espresso 添加到您的开发工具中，请考虑一下那些必须手动测试应用程序的人。

如果你有兴趣了解更多关于浓缩咖啡测试的知识，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇关于浓缩咖啡测试的文章很有趣，你可能会喜欢…

[在你的应用中使用 kot Lin](https://steelkiwi.com/blog/using-kotlin-your-android-production-code/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

帖子[简介:浓缩咖啡测试](https://apiumhub.com/tech-blog-barcelona/introduction-espresso-testing/)最早出现在 [Apiumhub](https://apiumhub.com) 上。