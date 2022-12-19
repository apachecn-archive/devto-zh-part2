# 在 Android Studio 布局编辑器中模拟 iOS/XCode StackView

> 原文：<https://dev.to/codeprototype/mimic-iosxcode-stackview-in-android-studio-layout-editor--507h>

在 Android Studio 的最近版本中，约束布局已被提升为 Android 应用程序的默认和首选布局。[将](https://www.youtube.com/watch?v=XamMbnzI5vE&index=16&t=0s&list=PLWz5rJ2EKKc_w6fodMGrA1_tsI3pqPbqa)描述为“由于灵活的约束系统，这是一种定义复杂布局而不需要嵌套视图层次的伟大的新方法”。当我们创建一个包含空活动的新项目时，Android Studio 默认在根级别生成一个 ConstraintLayout。因此，尽管仍然支持像过去一样在 Android 中使用 XML 构建 UI，但布局编辑器现在是最重要的。这一点在 [Android 开发者页面上有所体现，用布局编辑器构建用户界面](https://developer.android.com/studio/write/layout-editor)

以下是摘录:

`"In the Layout Editor, you can quickly build layouts by dragging UI elements into a visual design editor instead of writing the layout XML by hand. The design editor can preview your layout on different Android devices and versions, and you can dynamically resize the layout to be sure it works well on different screen sizes. The Layout Editor is especially powerful when building a new layout with ConstraintLayout".`

当我试图在 Android 上实现下面的屏幕时，就像我在 iOS 上做的那样

[![Numeric code verifier](../Images/a6b345d90ebbdff702c10aa07832161c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ur5eDek2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xocw64889bq0bpxzbbpv.gif)

(顺便在这里分享一下[https://medium . com/@ kevinle/build-an-IOs-front-end-to-verify-a-numeric-code-using-rx swift-d 776418 fade 6](https://medium.com/@kevinle/build-an-ios-front-end-to-verify-a-numeric-code-using-rxswift-d776418fade6))

在 XCode 中，有一个 StackView 功能。要在这种情况下使用它，我们只需多重选择这 6 个`TextFields`，点击`Embed in Stack`按钮，为`StackView`设置约束，并决定如何分配子视图。使用`StackView`的优点是不需要指定每个子视图的单独约束，所有约束都可以来自故事板。

[![XCode StackView](../Images/add3d28f9bb6ed064defddd15756498c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RlzRuvLp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hp1nugrcjgaffuvtu8wo.png)

Android Studio 是否从 XCode 复制了这个功能还存在争议，但不管怎样，ConstraintLayout 和布局编辑器现在已经得到了提升。所以让我们看看如何在 Android Studio 中以与 XCode 中相同的方式来做上面的例子。

1/.创建一个新的空白布局文件，指定`ConstraintLayout`为根。
2/。打开`Palette`，选择`LinearLayout`(水平)并将其放入。你会看到`LinearLayout`占据了整个窗口。现在还可以。
3/。在`ComponentTree`中选择`LinearLayout`，将左、右、上、下约束分别设置为`32 unit`，layout_width match_constraint，layout_height 185 dp。这个 layout_height 只是临时的。

[![Step 3](../Images/93d60c4b70934c7656bafe88626374e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QuQ8noq1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1jpwqblv4ejbk7ckk1bq.png)

4/.从`Palette`处，选择`Number Text`并放入`LinearLayout`内。
5 个/件。它将占用 LinearLayout 的整个宽度。
6/。第二个`Number Text`再做一遍。现在每个都是宽度的 50%。
7/。再重复 4 次。将会有 6 个大小相等的`EditTexts`占据它们的母体`LinearLayout`的整个宽度。
8/。在`ComponentTree`中，点击`LinearLayout`，将 layout_height 改为`Wrap_content`。您也可以使用垂直滑块上下移动`LinearLayout`。就我而言，我把它提高到了 25%。你应该有类似下图的东西:

[![Step 8](../Images/d0e4faccd13824b86009b2038f816086.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--maDt0bZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iw9kf9avek6kwl0tzza0.png)

9/.最后，对于每个`EditText`，点击“查看所有属性”，将`textSize`设置为`36 sp`，将`maxLength`设置为`1`，勾选“重力- >中心 _ 垂直”和“重力- >中心 _ 水平”

10/.现在快跑。它将看起来像 iOS 版。但是我们不会看到光标自动移动到下一个数字。这是下一部分。

**结论**

对于同时使用 Android 和 iOS 的开发者来说，在两个平台上工作时经历相同的体验是有益的。过去，iOS 和 Android 的方法是正交的。但是有了 Android Studio 中的`ConstraintLayout`和`Layout Editor`，这是缩小差距的一步。

如果你读了我在上面的 iOS 上的文章，你会看到我展示的实现方式是使用 RxSwift。对于 Android，我会展示 RxJava。这将进一步缩小差距。请继续关注。