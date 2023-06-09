# 在 10 行中检查 Android 上的网络连接

> 原文：<https://dev.to/varunbarad/check-network-connectivity-on-android-in-10-lines-16e5>

你是否曾经想要根据用户是否连接到网络来显示不同的内容？嗯，Android 提供了一种简单的方法来检查活跃的网络连接。

注:这是检查设备是否连接到网络(而不是它是否连接到互联网)。

Android 提供的检查网络连接的方法只有 10 行代码。但是我是一个懒惰的程序员，我只在一个 helper 方法中写一次代码，然后在任何需要检查连通性的地方使用这个单行方法调用。

正如您在下面的代码块中所看到的，将连通性检查逻辑封装在一个函数中是非常容易的。

```
// Java code sample

public class ConnectivityHelper {
  public static boolean isConnectedToNetwork(Context context) {
    ConnectivityManager connectivityManager =
        (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);

    boolean isConnected = false;
    if (connectivityManager != null) {
      NetworkInfo activeNetwork = connectivityManager.getActiveNetworkInfo();
      isConnected = (activeNetwork != null) && (activeNetwork.isConnectedOrConnecting());
    }

    return isConnected;
  }
} 
```

```
 // Kotlin code sample

// The next line should be the first statement in the file
@file:JvmName("ConnectivityHelper") // This line is only needed if you don't want caller statement in Java to change

fun Context.isConnectedToNetwork(): Boolean {
  val connectivityManager = this.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager?
  return connectivityManager?.activeNetworkInfo?.isConnectedOrConnecting() ?: false
} 
```

您还需要将`ACCESS_NETWORK_STATE`权限添加到您的清单中，如下所示。

```
<manifest ...>

  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <application ...>
    ...
  </application>
</manifest> 
```

### 搞定

现在，在任何需要检查连接的地方，您只需要

```
// Java code sample
if (ConnectivityHelper.isConnectedToNetwork(context)) {
  // Show the connected screen
} else {
  // Show disconnected screen
} 
```

```
// Kotlin code sample
if (context.isConnectedToNetwork()) {
  // Show the connected screenn
} else {
  // Show disconnected screen
} 
```

## 进一步阅读

官方文档是找到更多相关信息的好地方。

如果你知道更好的方法，[联系我](https://varunbarad.com/contact)或者发推特给我 [@varun_barad](https://twitter.com/varun_barad) 。你甚至可以把你想知道的任何其他话题的想法发给我。

### 备注

感谢 Giorgos Neokleous 建议在 Kotlin 中创建一个扩展函数。