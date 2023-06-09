# 从 Android 隐含意图中移除自我

> 原文：<https://dev.to/daisy1754/removing-self-from-android-implicit-intent--4880>

Android 提供了一个[隐式意图](https://developer.android.com/guide/components/intents-common.html)来基于用户的意图打开应用程序(例如，我想拍照)。这是一个简单但对用户非常有用的功能。

然而，当应用程序可以处理多个隐含意图时，这可能会很棘手。例如，如果你开发一个照片分享应用程序，你的应用程序可以同时成为“导出图像”意图的接收者和发送者。当你的用户试图从你的应用程序中导出图像时，如果你的应用程序也在接收者列表中，那就很尴尬了。

为了避免这种情况，您可以`queryIntentActivities`并显式地过滤意图，如下所示:

```
public static Intent createChooser(Context context, Intent target, CharSequence title) {
  List<ResolveInfo> resolveInfoList = context.getPackageManager().queryIntentActivities(
      target, PackageManager.MATCH_DEFAULT_ONLY);
  if (resolveInfoList.isEmpty()) {
    return Intent.createChooser(target, title);
  }

  Collections.sort(
      resolveInfoList, 
      new ResolveInfo.DisplayNameComparator(context.getPackageManager()));
  List<Intent> targetIntents = new ArrayList<Intent>();
  for (ResolveInfo resolveInfo : resolveInfoList) {
    if (context.getApplicationContext().getPackageName().equals(
        resolveInfo.activityInfo.packageName)) {
      continue;
    }
    Intent intent = new Intent(target);
    intent.setPackage(resolveInfo.activityInfo.packageName);
    intent.setClassName(
        resolveInfo.activityInfo.packageName, resolveInfo.activityInfo.name);
    targetIntents.add(intent);
  }

  Intent chooserIntent = Intent.createChooser(targetIntents.remove(0), title);
  chooserIntent.putExtra(
      Intent.EXTRA_INITIAL_INTENTS, 
      targetIntents.toArray(new Parcelable[]{}));
  return chooserIntent;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode