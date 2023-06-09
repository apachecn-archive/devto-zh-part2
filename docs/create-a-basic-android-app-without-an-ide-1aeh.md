# 创建一个没有 IDE 的基本 Android 应用程序

> 原文：<https://dev.to/oktadev/create-a-basic-android-app-without-an-ide-1aeh>

几乎每个 Android 教程都使用 Android Studio 来创建和开发应用程序。这对于学习来说不是很好，因为你看不到事物是如何工作的，也就是说

*   组成 Android Studio 项目的组件
*   构建的设置和配置方式
*   源由哪些部分组成

软件开发是关于文件的，在本教程中，我们将浏览一个基本 Android 项目中的每个文件——首先检查 Android Studio 输出的内容，然后从头构建一个 Android 项目。我们不会假设任何以前的 Android 经验，只会一点 Java。

注意:我将在 Windows 上这样做，但大多数指令应该在其他平台上工作。

## 分解你的 Android Studio 项目

当你开始一个完全裸露的项目时，这就是 Android Studio 所创建的。

[![Bare project file tree](img/c31aa9901918dd195a3b58b86c2d29c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GwUWP55k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/bare-project-576de8b3deab805ecdcba8d5c028fd9dec71b9f4c87b56778af991fcd55cbe26.png)

首先要注意的是，大多数文件都涉及 Gradle，即用于配置和执行构建的系统。没有任何 Gradle 文件我们还有什么？

[![Bare project file tree without Gradle](img/a3411fc88ddb83581fb7b9f82dceda21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--njpPLUXU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/bare-project-without-gradle-094673d3348e92661dfdc1a61861fa0a97cb19f3e12b159664bcae18268f0269.png)

只有三个文件夹和三个文件。显然，Android 项目的主要复杂性在于构建系统。

我们通过看*来看看有哪些文件不包含在源代码控制中。gitignore* 。

[![gitignore file contents](img/29af0c3c1c8c98803eced3865c1bd819.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---573ue_u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/gitignore-7c303ed5e232c95b245e721d1144fbf66df97d81dec320509055a3d5e9162a3e.png)

所以`MyApplication.iml`并不重要。如果你[谷歌一下](https://stackoverflow.com/questions/30737082/what-are-iml-files-in-android-studio)的 iml 文件是什么，你会看到它们被 Android Studio 使用，并且可以从`.idea/`的配置中重新生成。

另外，`local.properties`也不重要，还有`build/`。那给我们留下了什么？只有`app/`文件夹和`.idea/`中的一些文件，那里是 IntelliJ(Android Studio 构建于其上)存储配置文件的地方。

在`app`文件夹中，您会发现两个目录和三个文件:

*   `libs/`，空无一物
*   `src/`，哪个不是
*   `.gitignore`
*   `build.gradle`
*   `ProGuard`

ProGuard 通过移除未使用的库来帮助缩小您的最终 APK。不需要这个文件(其实都注释掉了)。如果您还不知道,`.gitignore`是用于源代码控制。所以只有`src/`和`build.gradle`才是重要的。

`src/`包含你的 Java 源代码，你使用的资源，比如布局和配置文件，还有`AndroidManifest`告诉 Android 你的应用是什么。`build.gradle`告诉 Gradle 如何使用 Gradle Android 插件将你的源代码转换成 APK。

为了看到所有这些，让我们从头开始构建我们的代码库，首先安装 SDK，然后初始化 gradle，转换为 Android 版本，最后编写应用程序代码。

## Android SDK 入门

对于这个项目，你需要下载 Android SDK。这只是一个 ZIP 文件。转到[正常安装页面](https://developer.android.com/studio/)并向右滚动到命令行工具的底部。在那里你可以找到只有 150 兆左右的压缩文件。提取并设置您的`ANDROID_SDK_ROOT`环境变量到提取的位置。

就是这样！格雷迪应该会自动捡起来。(注意:Gradle 将 SDK 位置存储在`local.properties`文件中，正如我们之前看到的，它没有保存到源代码控制中)。

## 初始化梯度

为了从头开始我们的项目，我们使用 Gradle 初始化一个文件夹。首先安装 Gradle。我从手册部分下载了二进制版本，并将`bin`文件夹添加到我的`PATH`中。

`gradle`命令现在应该可以从你的命令行工作了。注意:您还需要安装 Java 7 或更高版本。这是你用`gradle init`初始化一个空文件夹时看到的。

[![Gradle init output](img/14ea602c86c98b0f813037ba4cff04d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NKQxT6WJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/gradle-init-2b9194f1f68c141b53d1fac698d953d083ddc7e3c05ee6f645c6715d3c671e67.png)

看到 Android Studio 项目输出中的所有这些文件了吗？关于这些文件的详细解释，请参见 [Gradle create build guide](https://guides.gradle.org/creating-new-gradle-builds/) 。

## 创建一个 Android 版本

接下来，我们需要设置我们的项目来构建 Android。第一步是更改`settings.gradle`以简单地包含 app 模块(它只是一个文件夹)。

```
include ':app' 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将以下内容放入您的根目录`build.gradle`。

```
buildscript {

    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.3'
    }
}

allprojects {
    repositories {
        google()
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
} 
```

Enter fullscreen mode Exit fullscreen mode

这主要定义了从哪里下载我们的 Gradle 库。

接下来，创建`/app`目录并将以下内容放入`app/build.gradle`。

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 25
    defaultConfig {
        applicationId "com.example.karl.myapplication"
        minSdkVersion 16
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation 'com.android.support.constraint:constraint-layout:1.1.2'
    implementation 'com.android.support:appcompat-v7:253.1'
} 
```

Enter fullscreen mode Exit fullscreen mode

这使用了 Android Gradle 插件(com.android.application)并设置了一些值，如 SDK 版本和 Proguard(优化了我们的输出大小)。此外，在 dependencies 部分，它给出了我们想要导入的任何库(这里我们导入了两个库，它们都用于稍后构建我们的接口)。

现在创建`app/src/main/res/values/styles.xml`，我们将使用它来设置应用程序的主题。

```
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
    </style>

</resources> 
```

Enter fullscreen mode Exit fullscreen mode

最后把下面的放进`app/src/main/AndroidManifest.xml` :

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.karl.myapplication">

    <application
        android:label="Demo App"
        android:theme="@styles/AppTheme">

        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest> 
```

Enter fullscreen mode Exit fullscreen mode

这定义了我们的应用程序的包、标签和主要活动。

现在，当您运行`gradlew build`时，您应该看到构建成功。在`app/build/outputs/apk/debug`你应该会看到`app-debug.apk`。您刚刚从头开始建立了一个 Android 版本！

要部署它，只需插上手机说`gradlew installDebug`(并启用 USB 调试[([https://www . how togeek . com/129728/how-to-access-the-developer-options-menu-and-enable-USB-Debugging-on-Android-4.2/)](https://www.howtogeek.com/129728/how-to-access-the-developer-options-menu-and-enable-usb-debugging-on-android-4.2/)))。然后，您应该会看到一个名为 Demo App 的新应用程序。当你运行它的时候它会崩溃，因为你还没有写任何 Java 代码！

## 编写 Java 应用程序

构建完成后，接下来我们需要编写 Java。为此，我们只需要两个文件:主活动 Java 文件和布局 XML。

将以下内容放入`app/src/main/java/com/example/karl/myapplication/MainActivity.java`。

```
package com.example.karl.myapplication;

import android.app.Activity;
import android.os.Bundle;

public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它只是创建了一个新的活动(Android 中类似核心流程的想法)并将视图设置为 Resources 文件夹中的布局。

把这个放进`app/src/main/res/layout/activity_main.xml`。

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout> 
```

Enter fullscreen mode Exit fullscreen mode

这就创造了一个“Hello World！”信息中心-屏幕。

现在运行`gradlew build`，您应该会再次看到构建成功。使用`gradlew installDebug`安装到您的手机，您应该看到以下内容:

[![Hello world Android screen](img/3576ec105ddf5c69cf071c7ecbf78f90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xG_TGdZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/hello-world-2d12ddd0d978d4bc09026451420fe92032ea08c6fef8f64778c3fceb3854d279.png)

你刚刚做了一个除了文本编辑器什么都没有的 Android 应用程序:)。

## 用 Okta 添加认证

大多数现代应用程序都需要某种程度的安全性，因此了解如何简单方便地建立身份验证是值得的。为此，我们将使用 [OktaAppAuth](https://github.com/okta/okta-sdk-appauth-android) 包装器库。

### 为什么是 Okta？

在 Okta，我们的目标是让[身份管理](https://developer.okta.com/product/user-management/)比你习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

你被卖了吗？[注册一个永久免费的开发者帐户](https://developer.okta.com/signup/)，完成后，请回来，这样我们可以了解更多关于构建安全移动应用的信息！

### Java 中的认证

创建一个名为`LoginActivity.java`的新活动，并将其放在与 MainActivity 相同的文件夹中。

```
package com.example.karl.myapplication;

import android.app.Activity;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.util.Log;

import com.okta.appauth.android.OktaAppAuth;
import net.openid.appauth.AuthorizationException;

public class LoginActivity extends Activity {

    private OktaAppAuth mOktaAuth;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mOktaAuth = OktaAppAuth.getInstance(this);

        // Do any of your own setup of the Activity

        mOktaAuth.init(
            this,
            new OktaAppAuth.OktaAuthListener() {
                @Override
                public void onSuccess() {
                    // Handle a successful initialization (e.g. display login button)
                }

                @Override
                public void onTokenFailure(@NonNull AuthorizationException ex) {
                    // Handle a failed initialization
                }
            }
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将初始化`OktaAppAuth`对象并处理成功或失败条件。接下来，将`AndroidManifest.xml`改为指向`LoginActivity`而不是`MainActivity`。

现在将以下内容添加到`app/build.config`的`defaultConfig`部分。

```
android.defaultConfig.manifestPlaceholders = [
            "appAuthRedirectScheme": "com.okta.example"
        ] 
```

Enter fullscreen mode Exit fullscreen mode

最后，将以下内容添加到同一个文件的依赖项中:

```
implementation 'com.okta.android:appauth-android:0.1.0' 
```

Enter fullscreen mode Exit fullscreen mode

应该可以建立和部署。您可以使用`logcat`来查看后台发生的事情。查看主库类的[源代码，我们看到我们需要使用的标签是“OktaAppAuth”。](https://github.com/okta/okta-sdk-appauth-android/blob/master/library/src/main/java/com/okta/appauth/android/OktaAppAuth.java)

[![Logcat output](img/8a149e083fca031c2f2f48c92f0cd6bf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pnpCkJe0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/logcat-output-1770d20f94ce0b85227f1d650480d2de4acf49607fb9448a30a4e6a473a85dcd.png)

在尝试创建服务之后，我们得到一个`Configuration was invalid`错误。我们需要将我们的应用程序连接到 Okta 帐户。

### 连接 Okta 进行认证

因为您已经有了一个 [Okta 开发者帐户](https://developer.okta.com/signup/)，您可以直接进入配置。从开发人员控制台中，选择应用程序选项卡，然后选择新建应用程序。选择本机，然后单击下一步。这些字段应该会自动正确填充。最重要的部分是重定向 URL。单击完成。

在分配选项卡上，单击分配下拉列表，然后选择分配给组。单击“所有人”组旁边的“分配”。现在，Okta 组织中的任何人都可以通过应用程序的身份验证。

你现在应该有足够的空间来填充`app/src/main/res/raw/okta_app_auth_config.json`。

```
{
  "client_id": "{clientIdValue}",
  "redirect_uri": "{redirectUriValue}",
  "scopes": ["openid", "profile", "offline_access"],
  "issuer_uri": "https://dev-628819.oktapreview.com/oauth2/default"
} 
```

Enter fullscreen mode Exit fullscreen mode

将`app/build.gradle`中的`appAuthRedirectScheme`字段更改为重定向 URI 的基础，例如`"appAuthRedirectScheme": "com.oktapreview.dev-628819"`。

配置现在应该完成了！如果你像以前一样`gradlew installDebug`并执行 logcat，当你打开应用程序时，你应该不会再看到错误，应该会看到一条消息说`Warming up browser instance for auth request`。

### 设置您的登录页面

让我们在登录页面上添加一个按钮和进度条。创建 app/src/main/RES/layout/activity _ log in . XML .

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".LoginActivity">

    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical"
        android:gravity="center">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:paddingBottom="8pt"
                android:text="Demo App"
                style="@style/Base.TextAppearance.AppCompat.Title"/>

            <ProgressBar
                    android:id="@+id/progress_bar"
                    style="@style/Widget.AppCompat.ProgressBar.Horizontal"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:indeterminate="true"/>

            <Button
                android:id="@+id/auth_button"
                style="@style/Widget.AppCompat.Button.Colored"
                android:text="Login"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:visibility="gone" />

    </LinearLayout>

</android.support.constraint.ConstraintLayout> 
```

Enter fullscreen mode Exit fullscreen mode

最初，按钮是隐藏的。一旦 Okta 完成初始化，我们将显示它(并隐藏进度条)。为此，将以下代码放入`LoginActivity.java`中的 onSuccess()方法。

```
 findViewById(R.id.auth_button).setVisibility(View.VISIBLE);
    findViewById(R.id.progress_bar).setVisibility(View.GONE); 
```

Enter fullscreen mode Exit fullscreen mode

最后，在 Okta 初始化之前，将布局设置为我们刚刚创建的 XML。

```
 setContentView(R.layout.activity_login); 
```

Enter fullscreen mode Exit fullscreen mode

当你运行应用程序时，你应该会看到一个带有登录按钮的标题。

[![Main screen for Demo App](img/73b3161812e75af5e6fe8101e8be3965.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x-xR1ueR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/main-screen-demo-f4f07697a9028fbe8eb781c9a73c23445530a73313b134b1b190df63ae882ad6.png)

### 连线登录

为了获取登录详细信息，即用户名/密码，我们可以使用默认页面。首先创建我们在`AuthorizedActivity.java` :
获得授权时的登陆页面

```
package com.example.karl.myapplication;

import android.content.Intent;
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.widget.Button;
import android.view.View;

import com.okta.appauth.android.OktaAppAuth;
import static com.okta.appauth.android.OktaAppAuth.getInstance;
import net.openid.appauth.AuthorizationException;

public class AuthorizedActivity extends Activity {

    private OktaAppAuth mOktaAuth;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mOktaAuth = getInstance(this);

        setContentView(R.layout.activity_authorized);

        Button button = (Button) findViewById(R.id.sign_out);
        button.setOnClickListener(new View.OnClickListener()
{
        @Override
        public void onClick(View v)
        {
            mOktaAuth.logout();

            Intent mainIntent = new Intent(v.getContext(), LoginActivity.class);
            mainIntent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
            startActivity(mainIntent);
            finish();
        }
    }
      );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在按钮上附加了一个监听器，让我们退出并返回到登录页面。现在在`activity_authorized.xml`中放入

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".LoginActivity">

    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical"
        android:gravity="center">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center"
                android:paddingBottom="8pt"
                android:text="Authorized"
                style="@style/Base.TextAppearance.AppCompat.Title"/>

            <Button
                android:id="@+id/sign_out"
                style="@style/Widget.AppCompat.Button.Colored"
                android:text="Logout"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_gravity="center" />

    </LinearLayout>

</android.support.constraint.ConstraintLayout> 
```

Enter fullscreen mode Exit fullscreen mode

与登录页面一样，它只是一个带有按钮的标题。通过在 onCreate 方法的末尾放置以下内容来连接`LoginActivity.java`中的登录按钮。

```
Button button = (Button) findViewById(R.id.auth_button);
button.setOnClickListener(new View.OnClickListener()
    {
        @Override
        public void onClick(View v)
        {
            Intent completionIntent = new Intent(v.getContext(), AuthorizedActivity.class);
            Intent cancelIntent = new Intent(v.getContext(), LoginActivity.class);

            cancelIntent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mOktaAuth.login(
                v.getContext(),
                PendingIntent.getActivity(v.getContext(), 0, completionIntent, 0),
                PendingIntent.getActivity(v.getContext(), 0, cancelIntent, 0)
            );
        }
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你点击登录按钮时，你会看到一个 Okta 登录页面，询问你的详细信息。

[![Okta login screen](img/fd17d9dba421896be675526dcf556fc7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pS5UTqFE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/okta-login-screen-768b9d673c71580195af2cc73a6c1c52b2ab540f2017b2488915364ccc229efa.png)

如果您输入的用户详细信息与我们之前所做的申请相符(您的 Okta 门户凭据应该有效)，您将被带到授权页面。

[![Authorized screen](img/5c1318c41698c01ad81898e3c9224fed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oYrz1J5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/build-android-app-without-ide/authorized-screen-a502b99ce0324894ca9a62a773c9fdd861b8f7a778f7f95be6542b7b049a64c6.png)

单击注销按钮应该会将您带回我们的第一个屏幕。

授权到此为止！

大多数人认为你需要 Android Studio 来制作一个 Android 应用程序。在这篇文章中，你打破了这种观念，从头开始构建了一个 Android 应用程序。通过一些配置和少量代码，您就可以使用 OktaAppAuth 库将身份验证集成到您的应用程序中。然后，您创建了一个只有经过身份验证的用户才能看到的视图。感谢 Okta，从这里开始，您可以安全地构建您的应用程序的其余部分，因为您知道身份验证已得到处理。

## 了解有关 Java 和安全应用开发的更多信息

我希望你喜欢这篇关于如何在没有 IDE 的情况下构建一个基本 Android 应用的教程。你可以在 GitHub 的[https://github.com/oktadeveloper/okta-android-example](https://github.com/oktadeveloper/okta-android-example)找到本教程中创建的例子

我们已经写了一些其他很酷的 Spring Boot 和反应教程，如果你感兴趣的话可以看看。

*   [在 10 分钟内为任何网页添加认证](https://dev.to/oktadev/add-authentication-to-any-web-page-in-10-minutes-jhk-temp-slug-9795678)
*   [与 Spring Boot 和 React 的良好发展](https://developer.okta.com/blog/2017/12/06/bootiful-development-with-spring-boot-and-react)
*   [构建一个 React 原生应用，并使用 OAuth 2.0 进行认证](https://developer.okta.com/blog/2018/03/16/build-react-native-authentication-oauth-2)

如果您有任何问题，请不要犹豫在下面留下评论，或者在我们的 [Okta 开发者论坛](https://devforum.okta.com/)上询问我们。如果你想看更多像这样的教程，请在推特上关注我们 [@oktadev](https://twitter.com/oktadev) ！