# 用 Kotlin 在 Android 中构建一个基本的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-basic-crud-app-in-android-with-kotlin-3nlj>

Kotlin 最近获得了谷歌的官方 Android 支持地位，但对于许多开发人员来说，这仍然很难理解。最好的开始方式是自己创建一个完整的应用程序，这将在本教程中完成。在本教程中，您将使用 Spring Boot 作为支持 Android (+ Kotlin)移动应用程序的 API。Spring Boot 是用最少的代码创建健壮的 REST API 的好方法。

我假设你有一些 Java 经验，并且至少已经尝试过创建一个 Android 应用程序。如果你没有任何 Android 经验，你应该可以跟上，但你可能需要谷歌一些东西。

如果你想直接看到结尾，这里有完整的代码。

开始之前，我们先来谈谈科特林。

## 科特林 vs Java

科特林在新来者看来很奇怪。它类似于你可能见过的其他语言，但是有些东西看起来不像，通常是因为它太简洁了！

不要惊慌——因为它是如此的可扩展，所以有许多方法来编写相同的代码，并且有许多其他语言中没有的快捷方式。例如，你经常会看到花括号被用作函数参数:

```
dialogBuilder.setPositiveButton("Delete", { dialog, whichButton ->
    deleteMovie(movie)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是创建一个匿名函数(a *lambda* )并将其传入。这个函数接受两个参数，这里是推断出来的。看看对等的(JRE 8 之前)Java 代码:

```
dialogBuilder.setPositiveButton("Delete",
    new DialogInterface.OnClickListener() {
        public void onClick(DialogInterface dialog, int which) {
            deleteMovie(movie);
        }
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

(当然现在 Java 8 也有 [lambdas 也有](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/Lambda-QuickStart/index.html))。

下面是我们将会用到的另一个例子:

```
class MovieViewHolder(val view: View) : RecyclerView.ViewHolder(view) 
```

Enter fullscreen mode Exit fullscreen mode

为了理解这一点，你必须知道几件事:

用括号声明一个类(即`(view: View)`)意味着你声明了这个类的*主构造函数*(是的，还有[次构造函数](https://kotlinlang.org/docs/reference/classes.html))。冒号`:`类似于`implements`或`extends`，但实际上是关于[接口](https://kotlinlang.org/docs/reference/interfaces.html)。主构造函数中声明的任何东西都自动声明为属性(成员变量)。

为了清楚起见，这是等价的 Java:

```
public static class MovieViewHolder extends RecyclerView.ViewHolder {
    public final View view;
    public MovieViewHolder(View v) {
        super(v);
        view = v;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

作为最后一个例子，看看下面的 bean:

```
package demo

data class Movie( val id: Int, val name: String ) 
```

Enter fullscreen mode Exit fullscreen mode

这是完整的文件。它用一个构造函数、两个只读属性(成员变量)声明一个类，并在构造函数中对它们赋值。然后`data`为我们所有的成员变量创建 getters 和 setters，以及`equals()`、`toString()`、[和其他](https://kotlinlang.org/docs/reference/data-classes.html) ( [如果你想看完整版的 Java 荣耀，请点击这里](https://gist.github.com/kewp/1efc1a4c406577342c43ccb258bf8739))。

现在你已经有了一些背景知识，让我们开始吧！

## 为你的 Android + Kotlin 项目创建 Spring Boot API

Spring Boot 官方教程建议你使用 [Initializr 网站](https://start.spring.io/)创建一个初始框架，但我发现从头开始构建项目更容易。

首先，用 Gradle 初始化一个空目录(确保您已经[安装了 Gradle](https://gradle.org/install/) ，并且它在命令行上可用)。

```
C:\Users\Karl\Kotlin-Spring>gradle init

BUILD SUCCESSFUL in 3s
2 actionable tasks: 2 executed
C:\Users\Karl\Kotlin-Spring> 
```

Enter fullscreen mode Exit fullscreen mode

你应该有两个文件夹和四个文件。

```
.
├── build.gradle
├── gradle
│ └── wrapper
│ ├── gradle-wrapper.jar
│ └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── settings.gradle

2 directories, 6 files 
```

Enter fullscreen mode Exit fullscreen mode

现在将`build.gradle`改为如下:

```
buildscript {
    ext.kotlin_version = '1.2.61' // Required for Kotlin integration
    ext.spring_boot_version = '2.0.2.RELEASE'
    repositories {
        jcenter()
    }
    dependencies {
        // Required for Kotlin integration
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        // See https://kotlinlang.org/docs/reference/compiler-plugins.html#kotlin-spring-compiler-plugin         
        classpath "org.jetbrains.kotlin:kotlin-allopen:$kotlin_version"
        classpath("org.jetbrains.kotlin:kotlin-noarg:$kotlin_version")
        classpath "org.springframework.boot:spring-boot-gradle-plugin:$spring_boot_version"
    }
}

// Required for Kotlin integration
apply plugin: 'kotlin'
// See https://kotlinlang.org/docs/reference/compiler-plugins.html#kotlin-spring-compiler-plugin
apply plugin: "kotlin-spring" 
apply plugin: 'kotlin-jpa'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

jar {
    baseName = 'kotlin-demo'
    version = '0.1.0-SNAPSHOT'
}

repositories {
    jcenter()
}

dependencies {
    // Required for Kotlin integration
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
    compile "org.jetbrains.kotlin:kotlin-reflect" // For reflection
    compile 'org.springframework.boot:spring-boot-starter-data-rest'
    compile 'org.springframework.boot:spring-boot-starter-data-jpa'
    compile 'com.h2database:h2'
} 
```

Enter fullscreen mode Exit fullscreen mode

这里导入了科特林和 Spring Boot 插件，声明了外部存储库，并添加了依赖库。

如果你以前没有使用过 Spring Boot，你应该知道它(或者说 Spring 框架)在运行时使用依赖注入。这意味着整个应用程序是基于您导入的库自动连接的。例如，在我们的`build.gradle`的结尾，你会看到数据 REST 和数据 JPA 库。当 Spring Boot 看到这两个库时，它会自动将您的应用程序配置为 REST 服务器。此外，由于包含了`H2`数据库库，Spring 将使用 [H2 数据库引擎](http://www.h2database.com/html/main.html)来持久化查询中的任何 REST 数据。

要拥有一个完整的 REST 应用程序，您需要做的就是用`@SpringBootApplication`注释定义一个类。你甚至不需要指定它的路径——Spring 会搜索它！

将以下内容放入`src/main/kotlin/demo/Application.kt` :

```
package demo

import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication

@SpringBootApplication
class Application

fun main(args: Array<String>) {
    SpringApplication.run(Application::class.java, *args)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你运行`gradlew bootRun`(在*nix 上运行`./gradlew bootRun`)，所有的东西都应该构建(并下载)完毕，你应该会在巨大的日志**中的某个地方看到启动的应用**。现在在另一个窗口运行`curl`，看看发生了什么。

```
C:\Users\Karl>curl localhost:8080
{
  "_links" : {
    "profile" : {
      "href" : "http://localhost:8080/profile"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，您已经用 Kotlin 创建了一个完全兼容的 REST 服务器，只需编辑两个文件！

## 用 Kotlin 添加对象

要创建对象，您只需要实体类和存储库。

在`Application.kt`旁边，把下面的内容放进`Model.kt`

```
package demo

import javax.persistence.*

@Entity
data class Movie(@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
                 val Id: Long,
                 val name: String) 
```

Enter fullscreen mode Exit fullscreen mode

这里您已经使用了[数据习语](https://kotlinlang.org/docs/reference/idioms.html#creating-dtos-pojospocos)来为所有属性创建 getters 和 setters，以及 [JPA 注释](https://spring.io/guides/gs/accessing-data-jpa/)来指定如何为您的实体生成 id。

**注意:**`Id`字段必须以大写 **I** 开头。如果没有，服务器在查询时不会返回 id 字段。这将会给你的客户端应用带来麻烦。

现在把这个放进`Repository.kt` :

```
package demo

import org.springframework.data.repository.CrudRepository

interface ItemRepository : CrudRepository<Movie, Long> 
```

Enter fullscreen mode Exit fullscreen mode

你做到了！令人难以置信的是，我们现在可以在这个服务器上执行任何 CRUD 操作，并且它会工作，将所有更改保存到数据库中。

```
C:\Users\Karl>curl -X POST -H "Content-Type:application/json" -d " {\"name\":\"The 40 Year Old Virgin\"} " localhost:8080/movies
{
  "name" : "The 40 Year Old Virgin",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/movies/1"
    },
    "item" : {
      "href" : "http://localhost:8080/movies/1"
    }
  }
}

C:\Users\Karl>curl localhost:8080/movies/1
{
  "name" : "The 40 Year Old Virgin",
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/movies/1"
    },
    "item" : {
      "href" : "http://localhost:8080/movies/1"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在您的 Kotlin 应用程序中加载初始数据

最后，让我们加载一些数据。和 Spring Boot 一样，一切都可以简单地完成。只需将以下内容放入`src/main/resources/data.sql`中，它就会在启动时运行。

```
INSERT INTO movie (name) VALUES
  ('Skyfall'),
  ('Casino Royale'),
  ('Spectre'); 
```

Enter fullscreen mode Exit fullscreen mode

要确认它工作，重启服务器并运行`curl localhost:8080/movies`。

您已经完成了后端工作。是时候建立客户了。

## 使用 Kotlin 构建您的 Android 应用程序

这需要几个步骤:首先你要用 Android Studio 创建一个空的 Kotlin 应用程序。然后您将使用[recycle view](https://developer.android.com/guide/topics/ui/layout/recyclerview)创建一个列表视图(带有添加、编辑和删除按钮)，用硬编码数据填充它。最后，您将使用[改进](https://square.github.io/retrofit/)将视图连接到您刚刚创建的其余后端。

在 Android Studio 中创建项目。请确保您使用的版本至少是 Android Studio 3。除了确保**包含 Kotlin 支持**之外，每个窗口都使用默认值。随你怎么命名这个项目——我把我的命名为“科特林垃圾”。最后，选择一个**空活动**。

当你点击顶部图标栏上的 Play 时，你应该会看到 Hello World(你可以插上你的手机或者在模拟器上运行它[。在线查看如何设置)。](https://developer.android.com/studio/run/managing-avds)

[![Hello World in Android](img/817fe87751a740b037dad3fa5cfa3833.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sfk1rCpk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/android-kotlin-crud/hello-world-android-72e1560cd6e6b760073155ca8463ed808dc957a5ee03966ebb39a2f0f6283bf6.png)

如果你在使用 Java 之前已经制作了一个 Android 应用程序，你会注意到唯一的区别是主活动:它被称为 **MainActivity.kt** ，而不是`MainActivity.java`，代码看起来有点不同。

```
package demo

import android.support.v7.app.AppCompatActivity
import android.os.Bundle

class MainActivity : AppCompatActivity() {

   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       setContentView(R.layout.activity_main)
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是不同之处:

1.  类没有被指定为`public`(在 Kotlin 中这是默认的)
2.  类型用冒号`:`指定——类是类型`AppCompatActivity`(或者更确切地说，它*实现* `AppCompatActivity`，就像你在 Java 中说的那样)而`savedInstanceState`是类型`Bundle`
3.  方法只是被调用`fun`而不是*函数*
4.  `override`不是注释
5.  问号意味着参数*是可选的*(这在 Java 中是不可能的)

最后一点是讨论 Kotlin 与 Java 的重要性时谈论最多的一点:这是该语言确保空安全的各种方式之一。

## 导入额外的 Android 库

您需要在应用程序的`build.gradle`文件中添加额外的库:一个用于回收器视图(稍后您将用到)，一个用于卡片视图，另一个用于浮动动作按钮。将这些物品放在`dependencies`部分的其他物品旁边。

```
implementation 'com.android.support:design:27.1.1'
implementation 'com.android.support:cardview-v7:27.1.1'
implementation 'com.android.support:recyclerview-v7:27.1.1' 
```

Enter fullscreen mode Exit fullscreen mode

Android Studio 应该会要求您现在*同步*。点击它，你会发现所有的东西都构建好了，没有错误。

**注意:**确保版本与其他支持库相同(例如 appcompat-v7:27.1.1)。此外，因为您将使用内置图标(您应该避免在将来这样做)，您还需要将以下内容放入您的`build.gradle`的`defaultConfig`部分。

```
vectorDrawables.useSupportLibrary = true 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Kotlin 中添加图标

你需要一些按钮图标——一个用于*添加*，另一个用于*刷新*。进入[材质图标](https://material.io/tools/icons/)网站，选择你喜欢的一个。我选择[添加按钮](https://material.io/tools/icons/?icon=add&style=baseline)到一半。当你点击它，一个灰色和蓝色的下载部分应该出现在左边的按钮。点击灰色框**选中图标**控件打开下载选项。现在应该有一个下拉框，您可以选择 Android 作为类型。

[![Change drop down to Android](img/4040c9eac58bf47c37f503b9c39d04e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--89MKRPpi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/android-kotlin-crud/download-icons-31857d19b0c2ffab00b0b54e8978322506fbfddd2b005731125919f5084b3796.png)

将颜色改为白色并下载 PNG 选项。将 ZIP 文件的内容解压到`app/src/main`(您应该看到 ZIP 文件中有一个`res`文件夹)。

现在你可以在你的布局中使用新图标了。它们被称为`baseline_add_white_36`之类的东西。

最后对*循环*图标做同样的事情，也是白色的。

## 创建视图 XML

每个列表项都需要一个 XML 视图。将以下内容放入`src/main/res/layout/list_item.xml`。

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"

    android:id="@+id/card_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginBottom="3dp"
    android:layout_marginLeft="5dp"
    android:layout_marginRight="5dp"
    android:layout_marginTop="5dp"
    android:padding="3dp"
    card_view:cardElevation="2dp"
    card_view:cardMaxElevation="2dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp">

        <TextView
            android:id="@+id/name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:padding="5dp"
            android:text="lastname"
            android:textSize="16dp" />

        <TextView
            android:id="@+id/btnDelete"
            android:layout_width="wrap_content"
            android:layout_height="35dp"
            android:layout_alignParentRight="true"
            android:drawableLeft="@android:drawable/ic_delete"
            android:padding="5dp" />

        <TextView
            android:id="@+id/btnEdit"
            android:layout_width="wrap_content"
            android:layout_height="35dp"
            android:layout_marginRight="2dp"
            android:layout_toLeftOf="@+id/btnDelete"
            android:drawableLeft="@android:drawable/ic_menu_edit"
            android:padding="5dp" />
    </RelativeLayout>

</android.support.v7.widget.CardView> 
```

Enter fullscreen mode Exit fullscreen mode

这里你使用的是一个[卡片视图](https://developer.android.com/guide/topics/ui/layout/cardview)，这是在 Android 中创建列表的流行方式。几乎所有的 XML 都是布局设置，以确保正确对齐。注意用于将这些连接到我们的 Kotlin 文件的`android:id`值。另外，我在编辑和删除按钮上使用了一些内置的 Android 图标。

**注意:**这不是推荐的做法，因为这些图标会在 Android Studio 版本之间变化——不如像我们之前那样下载图标！

现在来看看主要的活动 XML。这里是`src/main/res/layout/activity_main.xml`应该的样子。

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/rv_item_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/baseline_add_white_36"
        android:layout_gravity="bottom|end"
        app:elevation="6dp"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true"
        android:layout_margin="20dp"/>

</RelativeLayout> 
```

Enter fullscreen mode Exit fullscreen mode

这很简单。现在你已经有了一个 recycle 视图和一个相对布局中的浮动动作按钮，并且已经指定了`baseline_add_white_36`作为按钮的源。注意，回收器视图的 id 是`rv_list_item`(您很快就会用到它)。

## 向操作栏添加刷新

为了填充内容，让我们在操作栏上放置一个刷新按钮。这需要在`res/menu/buttons.xml` :
中添加一段新的 XML

```
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">

    <item
        android:id="@+id/refresh"
        android:icon="@drawable/baseline_loop_white_48"
        android:title="@string/refresh"
        app:showAsAction="ifRoom"/>

</menu> 
```

Enter fullscreen mode Exit fullscreen mode

注意它有一个名为**刷新**的 id。此外，我使用了 Android 图标网站上的*循环*图标(白色变体)——你必须像以前一样下载这个。另外，我使用了参考资料中的一个字符串，所以您必须更改`res/values/strings.xml` :

```
<resources>
    <string name="app_name">Kotlin Crud</string>
    <string name="refresh">Refresh</string>
</resources> 
```

Enter fullscreen mode Exit fullscreen mode

## 显示 Kotlin 中的列表

现在使用我们的视图显示一个项目列表。这样做的标准方式是相对较新的`RecyclerView`，它取代了最初的`ListView`。一个`RecyclerView`的基本思想是只创建足够显示在屏幕上的视图——如果屏幕能容纳五个项目，那么只创建五个。当您在列表中滚动时，这些视图被重用(回收)，用适当的(新的)值替换它们的内容。

你如何开始做这件事？你首先需要的是一颗豆子。姑且称之为`Movie.kt`。

```
package demo

data class Movie( val id: Int, val name: String ) 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**确保`package`与`MainActivity.kt`匹配。

那不容易吗？接下来你需要一个`Adapter`。这是一个有三个方法的类:一个返回总共显示了多少项(`getItemCount()`)，一个为特定项创建 Android 视图控件(`onCreateViewHolder()`)，一个用数据实例填充现有视图(`onBindViewHolder()`)。

把这个放进`MovieAdapter.kt`。

```
class MovieAdapter : RecyclerView.Adapter<MovieAdapter.MovieViewHolder>() {

    var movies: ArrayList<Movie> = ArrayList()

    init { refreshMovies() }

    class MovieViewHolder(val view: View) : RecyclerView.ViewHolder(view)

    override fun onCreateViewHolder(parent: ViewGroup,
                                    viewType: Int): MovieAdapter.MovieViewHolder {

        val view = LayoutInflater.from(parent.context)
                .inflate(R.layout.list_item, parent, false)

        return MovieViewHolder(view)
    }

    override fun onBindViewHolder(holder: MovieViewHolder, position: Int) {
        holder.view.name.text = movies[position].name
    }

    override fun getItemCount() = movies.size

    fun refreshMovies() {
        movies.clear()

        movies.add(Movie(0, "Guardians of the Galaxy"))
        movies.add(Movie(1, "Avengers: Infinity War"))
        movies.add(Movie(2,"Thor: Ragnorok"))

        notifyDataSetChanged()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当你将它粘贴到 Android Studio 时，它会将某些东西高亮显示为红色。您需要多次按 ALT-ENTER(在 Mac 上是 Option + Enter)来获取您需要的导入。最后，这是您应该拥有的导入列表:

```
import android.support.v7.widget.RecyclerView
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import kotlinx.android.synthetic.main.list_item.view.* 
```

Enter fullscreen mode Exit fullscreen mode

在`MovieAdapter.kt`发生了很多事情。除了实现实现`RecylcerView.Adapter`所需的三个方法，您还创建了一个名为`movies`的属性，一个列表，并在`init{}`构造函数中初始化它。此外，您声明了一个名为`MovieViewHolder`的内部类。这就是为每个需要显示的视图实例化的内容(在讨论的例子中，有五个视图)。如你所见，`onCreateViewHolder`实际上返回了这种类型的对象。这个类非常简单——它将一个`View`(现在也是一个属性)放入其构造函数中，并返回一个`Holder`类型的对象。这个对象就是您在使用`onBindViewHolder`填充数据时使用的对象——在我们的例子中，设置我们的显示文本。

乍一看，这确实很复杂。看待这一切的好方法如下:它如何连接到您的主代码类(即`MainActivity.kt`)，以及它如何连接到您在 XML 中定义的视图？

对于第一部分，主活动现在应该是这样的:

```
class MainActivity : AppCompatActivity() {

    lateinit var adapter:MovieAdapter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        adapter = MovieAdapter()    

        rv_item_list.layoutManager = LinearLayoutManager(this)
        rv_item_list.adapter = adapter
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这里你已经将`adapter`定义为一个 **lateinit** 变量- `lateinit`告诉 Kotlin 你想在创建后的某个阶段初始化它，而不是 Kotlin 类中的默认设置-通常你必须立即初始化。

在构造函数中，您将适配器的一个实例分配给这个属性(注意，在 Kotlin 中您不需要使用`new`)，并将两件事情分配给`rv_item_list`—[layout manager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager)(用于定位)，以及一个适配器(我们刚刚创建的)。

我们应该谈谈`rv_item_list`。这只是`activity_main.xml`中一个控件的 id，特别是 recyclerview。通常情况下，你需要使用`findViewById`(Android 开发者的痛苦)，但是使用 Kotlin，你可以直接指定它的名字。当 Android Studio 抱怨导入时，您按 ALT-ENTER(或您的平台等价物)它会自动导入`kotlinx.android.synthetic.main.activity_main.*`，将所有 id 带入名称空间。

最后，给`MainActivity` :
添加以下两个函数

```
override fun onCreateOptionsMenu(menu: Menu): Boolean {
    val inflater = menuInflater
    inflater.inflate(R.menu.buttons, menu)
    return true
}

override fun onOptionsItemSelected(item: MenuItem) = when (item.itemId) {
    R.id.refresh -> {
        adapter.refreshMovies()
        Toast.makeText(this.baseContext, "Refreshed", Toast.LENGTH_LONG).show())
        true
    }
    else -> {
        super.onOptionsItemSelected(item)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将膨胀您定义的菜单 xml，并将按钮绑定到您的适配器的刷新功能(并提供一个方便的 [toast](https://developer.android.com/guide/topics/ui/notifiers/toasts) 来说明它工作了)。

应该就是这样！运行我们的代码，您应该会看到以下内容。

[![Completed Android app with movie list](img/91dda96ae82a3dd9085ee439b4b48bbc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2nJEpr8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/android-kotlin-crud/movie-list-android-56b754978fce492ea4882557e03fa8bf4402ed2036ea80d056b60e6c0e0b339c.png)

## 连接您的 Android + Kotlin 应用

接下来，您需要用来自 API 服务器的数据替换硬编码的值，并将不同的按钮连接到它们各自的 API 调用。为此，您将使用 Square 的改造库。

首先将以下内容添加到您的`build.gradle`依赖项中:

```
implementation 'com.squareup.retrofit2:retrofit:2.3.0'
implementation 'com.squareup.retrofit2:converter-gson:2.3.0'
implementation 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'
implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
implementation 'com.squareup.okhttp3:logging-interceptor:3.9.1' 
```

Enter fullscreen mode Exit fullscreen mode

现在来看看当您调用服务器来获取电影列表时会发生什么:

```
C:\Users\Karl>curl http://localhost:8080/movies
{
  "_embedded" : {
    "movies" : [ {
      "name" : "Skyfall",
      "id" : 1,
      "_links" : {
        "self" : {
          "href" : "http://localhost:8080/movies/1"
        },
        "movie" : {
          "href" : "http://localhost:8080/movies/1"
        }
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我只展示了一个，因为它很长(Spring 遵循了 HATEOAS，它添加了到 json 响应的链接)。如您所见，响应被包装在一个`_embedded`对象中，您的电影作为一个列表出现在`movies`中。您需要在 Kotlin 模型中表示这一点，以便改型设计可以知道会发生什么。将`Movie.kt`改为:

```
import com.google.gson.annotations.SerializedName

data class Movie( val id: Int, val name: String )
data class MovieList (
    @SerializedName("movies" )
    val movies: List<Movie>
)
data class MovieEmbedded (
    @SerializedName("_embedded" )
    val list: MovieList
) 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要创建一个新的类来设置改造。姑且称之为`MovieApiClient.kt` :

```
import io.reactivex.Completable
import io.reactivex.Observable
import retrofit2.Retrofit
import retrofit2.adapter.rxjava2.RxJava2CallAdapterFactory
import retrofit2.converter.gson.GsonConverterFactory
import retrofit2.http.*

interface MovieApiClient {

    @GET("movies") fun getMovies(): Observable<MovieEmbedded>
    @POST("movies") fun addMovie(@Body movie: Movie): Completable
    @DELETE("movies/{id}") fun deleteMovie(@Path("id") id: Int) : Completable
    @PUT("movies/{id}") fun updateMovie(@Path("id")id: Int, @Body movie: Movie) : Completable

    companion object {

        fun create(): MovieApiClient {

            val retrofit = Retrofit.Builder()
                    .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                    .addConverterFactory(GsonConverterFactory.create())
                    .baseUrl("http://10.0.2.2:8080/")
                    .build()

            return retrofit.create(MovieApiClient::class.java)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，您使用注释定义了所有的端点以及它们的预期返回类型(`Completable`，是 [RxJava](https://github.com/ReactiveX/RxJava) 的一部分，意思是不返回任何东西)。您还声明了一个[伴随对象](https://kotlinlang.org/docs/reference/object-declarations.html#companion-objects)(类似于一个静态类)，它用我们的 API 的细节实例化了一个改造构建器。注意，基本 url 使用 IP `10.0.2.2`,它允许模拟器连接到本地主机。

现在在`MovieAdapter`中，修改头部以包含一个`context`属性(这样你就可以为我们的 API 结果添加祝酒词)以及添加一个用你之前的`create()`方法初始化的[惰性](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy)客户端属性。

```
class MovieAdapter(val context: Context) : RecyclerView.Adapter<MovieAdapter.MovieViewHolder>() {

    val client by lazy { MovieApiClient.create() }
    var movies: ArrayList<Movie> = ArrayList() 
```

Enter fullscreen mode Exit fullscreen mode

Lazy 接受一个函数(注意花括号)并说“当有人第一次试图使用这个属性时，运行这个函数并分配它”。

要初始化上下文，请更改`adapter` initialize 语句以包含主活动上下文:

```
adapter = MovieAdapter(this.baseContext) 
```

Enter fullscreen mode Exit fullscreen mode

现在将适配器中的`refreshMovies()`改为如下:

```
fun refreshMovies() {
    client.getMovies()
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({ result ->
                movies.clear()
                movies.addAll(result.list.movies)
                notifyDataSetChanged()
        },{ error ->
                Toast.makeText(context, "Refresh error: ${error.message}", Toast.LENGTH_LONG).show()
                Log.e("ERRORS", error.message)
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

所以你使用的是客户端的`getMovies()`函数，它被声明在`MovieApiClient.kt`的顶部。理解这里发生的事情本身就是一个完整的讨论。基本上，它使用了[反应式编程](http://reactivex.io/)，这是一种将异步事物连接在一起的新方法(比如调用外部 API)。

对于其余的访问方法，在`refreshMovies()`后加上以下内容:

```
fun updateMovie(movie: Movie) {
    client.updateMovie(movie.id, movie)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({ refreshMovies() }, { throwable ->
                Toast.makeText(context, "Update error: ${throwable.message}", Toast.LENGTH_LONG).show()
        })
}

fun addMovie(movie: Movie) {
    client.addMovie(movie)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({ refreshMovies() }, { throwable ->
                Toast.makeText(context, "Add error: ${throwable.message}", Toast.LENGTH_LONG).show()
        })
}

fun deleteMovie(movie: Movie) {
    client.deleteMovie(movie.id)
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe({ refreshMovies() }, { throwable ->
                Toast.makeText(context, "Delete error: ${throwable.message}", Toast.LENGTH_LONG).show()
        })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里您像以前一样使用改进的被动调用，但是当事情成功返回时只调用`refreshMovies()`(如果没有成功返回，则为错误干杯)。

## 在 Kotlin 中显示对话框

您需要做的最后一件事是显示各种输入情况的对话框:删除、编辑和添加。在这里，您将手动完成这项工作，因此您不必创建任何新的 XML。

在`MainActivity.kt`中增加以下功能:

```
fun showNewDialog() {
    val dialogBuilder = AlertDialog.Builder(this)

    val input = EditText(this@MainActivity)
    val lp = LinearLayout.LayoutParams(
            LinearLayout.LayoutParams.MATCH_PARENT,
            LinearLayout.LayoutParams.MATCH_PARENT)
    input.layoutParams = lp

    dialogBuilder.setView(input)

    dialogBuilder.setTitle("New Movie")
    dialogBuilder.setMessage("Enter Name Below")
    dialogBuilder.setPositiveButton("Save", { dialog, whichButton ->
        adapter.addMovie(Movie(0,input.text.toString()))
    })
    dialogBuilder.setNegativeButton("Cancel", { dialog, whichButton ->
        //pass
    })
    val b = dialogBuilder.create()
    b.show()
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的[对话框生成器](https://developer.android.com/guide/topics/ui/dialogs)用于显示一个标准的弹出窗口。您还手动添加了一个`EditText`控件，以便用户可以输入新的名称。侦听器是为正按钮和负按钮创建的(参见前面链接的添加按钮部分)，当正按钮出现时(一个对话框确认)，适配器的`addMovie`函数被调用。

为确保点击动作按钮时弹出该对话框，在`MainActivity.onCreate()`功能的底部添加以下内容:

```
fab.setOnClickListener{ showNewDialog() } 
```

Enter fullscreen mode Exit fullscreen mode

这里是其他对话框的代码，我们将把它们放入`MovieAdapter` :

```
fun showUpdateDialog(holder: MovieViewHolder, movie: Movie) {
    val dialogBuilder = AlertDialog.Builder(holder.view.context)

    val input = EditText(holder.view.context)
    val lp = LinearLayout.LayoutParams(
             LinearLayout.LayoutParams.MATCH_PARENT,
             LinearLayout.LayoutParams.MATCH_PARENT)
    input.layoutParams = lp
    input.setText(movie.name)

    dialogBuilder.setView(input)

    dialogBuilder.setTitle("Update Movie")
    dialogBuilder.setPositiveButton("Update", { dialog, whichButton ->
        updateMovie(Movie(movie.id,input.text.toString()))
    })
    dialogBuilder.setNegativeButton("Cancel", { dialog, whichButton ->
        dialog.cancel()
    })
    val b = dialogBuilder.create()
    b.show()
}

fun showDeleteDialog(holder: MovieViewHolder, movie: Movie) {
    val dialogBuilder = AlertDialog.Builder(holder.view.context)
    dialogBuilder.setTitle("Delete")
    dialogBuilder.setMessage("Confirm delete?")
    dialogBuilder.setPositiveButton("Delete", { dialog, whichButton ->
        deleteMovie(movie)
    })
    dialogBuilder.setNegativeButton("Cancel", { dialog, whichButton ->
        dialog.cancel()
    })
    val b = dialogBuilder.create()
    b.show()
} 
```

Enter fullscreen mode Exit fullscreen mode

要连接它们，将下面的代码添加到`onBindViewHolder`方法中:

```
holder.view.btnDelete.setOnClickListener { showDeleteDialog(holder, movies[position]) }
holder.view.btnEdit.setOnClickListener { showUpdateDialog(holder, movies[position]) } 
```

Enter fullscreen mode Exit fullscreen mode

你差不多完成了。你只需要给你的应用程序访问外部服务器(互联网)的权限。在您的`AndroidManifest.xml`中的`<application>`节点上方添加以下内容。

```
<uses-permission android:name="android.permission.INTERNET" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在运行你的应用程序。你应该能够添加，编辑和删除，所有持久化到后端，每个动作都有一个对话框来确认或与细节。您刚刚完成了一个完整的 CRUD 客户端应用程序解决方案！

[![Add a movie via a new dialog](img/4c5cde7ac2c1b0c70eb4ed07480065ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--33Lf8dq1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/android-kotlin-crud/new-movie-dialog-fe6a53796db4dfed57421a3368d07142c8a69b11f4eb5dbffc1e3eb6e98e46f3.png)

## 为使用 Android 和 Kotlin 的安全移动应用程序添加认证

大多数现代应用程序都需要某种程度的安全性，因此了解如何快速轻松地添加身份验证是值得的。为此，您将使用 [OktaAppAuth](https://github.com/okta/okta-sdk-appauth-android) 包装器库。

### 为什么是 Okta？

Okta 的目标是让[身份管理](https://developer.okta.com/product/user-management/)比你习惯的更容易、更安全、更可扩展。Okta 是一种云服务，允许开发人员创建、编辑和安全存储用户帐户和用户帐户数据，并将它们与一个或多个应用程序连接。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

注册一个永远免费的开发者账户，当你完成后，创建一个新的本地应用程序，记下客户端 id 并重定向 URI。

### 添加 OAuth 2.0 授权服务器

首先你需要把你的服务器变成一个 OAuth 资源(由 Okta 管理)。将以下内容添加到您的`build.gradle`中。

```
compile 'com.okta.spring:okta-spring-boot-starter:0.5.0'
compile 'org.springframework.boot:spring-boot-starter-security'
compile 'org.springframework.security.oauth:spring-security-oauth2:2.2.0.RELEASE'
compile 'org.springframework.security.oauth.boot:spring-security-oauth2-autoconfigure:2.0.1.RELEASE' 
```

Enter fullscreen mode Exit fullscreen mode

现在将下面的导入添加到您的`Application.kt`中。

```
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer 
```

Enter fullscreen mode Exit fullscreen mode

也用`@EnableResourceServer`注释类。然后创建一个名为`src/main/resource/application.properties` :
的文件

```
okta.oauth2.issuer=https://{yourOktaDomain}/oauth2/default
okta.oauth2.clientId={clientId} 
```

Enter fullscreen mode Exit fullscreen mode

登录 Okta 管理网站时，您可以在应用程序选项卡中获取客户端 ID 值。

现在，当您重新启动 Spring Boot 并尝试匿名访问您的 API 时，您将得到一个授权错误。

```
C:\Users\Karl>curl localhost:8080/movies
{"error":"unauthorized","error_description":"Full authentication is required to access this resource"} 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 Android AppAuth 插件

要让我们的应用程序使用 Okta，你需要使用 Android 的 AppAuth 插件。首先创建一个新的活动来容纳登录过程。

转到**新**->-**活动** - > **空活动**称之为`LoginActivity`。在创建的`activity_login.xml`中放置以下内容:

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
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
            android:id="@+id/auth_message"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:paddingBottom="8pt"
            android:text="Initializing authorization"
            style="@style/Base.TextAppearance.AppCompat.Medium"/>

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

现在在`AndroidManifest.xml`中，交换登录活动和主活动的名称，这样它就是启动时启动的登录。

[![Hello World in Android](img/f2445d91d5ee3a57a7190674b00e9282.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2EWK-ZUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/android-kotlin-crud/add-auth-android-18ddb6499363e3e80bdc94341f9c5663bfba5729899f1db291b25b851168849b.png)

您已经添加了一个进度条，它将一直保持到 Okta 的授权连接完成。一旦完成，你将隐藏它，然后显示一个登录按钮。为此，您需要更改`LoginActivity.kt`。但首先，将以下内容添加到您的`build.gradle`中。

```
implementation 'com.okta.android:appauth-android:0.1.0' 
```

Enter fullscreen mode Exit fullscreen mode

这将为 Android 引入 Okta AppAuth 库。您还需要将支持库版本更改为 **25.3.1** ，以便与该库兼容，因此将对其他版本(例如`27.1.1`)的任何引用更改为该版本。另外，将 minSdkVersion 更改为`16`，将目标 SDK 更改为`25`。最后，将以下内容添加到`defaultConfig` :

```
android.defaultConfig.manifestPlaceholders = [
    // match the protocol of your "Login redirect URI"
    "appAuthRedirectScheme": "com.oktapreview.dev-628819"
] 
```

Enter fullscreen mode Exit fullscreen mode

现在一切都应该建立良好。

接下来，将`LoginActivity.kt`的内容更改为以下内容:

```
class LoginActivity : AppCompatActivity() {

    private var mOktaAuth: OktaAppAuth? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        mOktaAuth = OktaAppAuth.getInstance(this)

        setContentView(R.layout.activity_login)

        mOktaAuth!!.init(
                this,
                object : OktaAppAuth.OktaAuthListener {
                    override fun onSuccess() {
                        auth_button.visibility = View.VISIBLE
                        auth_message.visibility = View.GONE
                        progress_bar.visibility = View.GONE
                    }

                    override fun onTokenFailure(ex: AuthorizationException) {
                        auth_message.text = ex.toString()
                        progress_bar.visibility = View.GONE
                        auth_button.visibility = View.GONE
                    }
                }
        )

        val button = findViewById(R.id.auth_button) as Button
        button.setOnClickListener { v ->
                val completionIntent = Intent(v.context, MainActivity::class.java)
                val cancelIntent = Intent(v.context, LoginActivity::class.java)

                cancelIntent.flags = Intent.FLAG_ACTIVITY_CLEAR_TOP

                mOktaAuth!!.login(
                        v.context,
                        PendingIntent.getActivity(v.context, 0, completionIntent, 0),
                        PendingIntent.getActivity(v.context, 0, cancelIntent, 0)
                )
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将尝试初始化 Okta 认证，并在完成后隐藏进度条和消息，显示一个登录按钮。现在，如果您尝试运行它，您会看到一个错误。我们需要在我们的项目中添加 Okta 应用程序细节

### 配置你的 Okta App

创建`app/src/main/res/raw/okta_app_auth_config.json`并将以下内容放入其中:

```
{
  "client_id": "{clientId}",
  "redirect_uri": "{redirectUriValue}",
  "scopes": ["openid", "profile", "offline_access"],
  "issuer_uri": "https://{yourOktaDomain}/oauth2/default"
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该从 Okta 管理控制台的应用程序中获得的`clientId`和`redirectUriValues`。

现在，当你重启应用程序时，你应该会看到一个登录按钮。

[![Login button added to application](img/9e61e3f1b57a2ff5f2d2f2b70c495b27.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SxMawUtW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets/blog/android-kotlin-crud/login-button-android-476e79cd6ef714761d76e5f913351793b73d9e5c8858017edabb8d62a31fe647.png)

按下它会带你到一个预建 Okta 登录屏幕。如果您使用 Okta 帐户的凭证登录，您将被允许进入我们的主要活动，但列表将是空的-您应该会得到一个 401 错误提示弹出窗口(未经授权的访问)。

### 添加授权进行改装

您需要从 AppAuth 库中获取访问令牌，并在进行 API 调用时传递它以进行改进。在`MainActivity.kt`内增加以下功能:

```
fun readAuthState(): AuthState {
    val authPrefs = getSharedPreferences("OktaAppAuthState", Context.MODE_PRIVATE)
    val stateJson = authPrefs.getString("state", "")
    return if (!stateJson!!.isEmpty()) {
        try {
            AuthState.jsonDeserialize(stateJson)
        } catch (exp: org.json.JSONException) {
            Log.e("ERROR",exp.message)
            AuthState()
        }

    } else {
        AuthState()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它使用[共享偏好](https://developer.android.com/reference/android/content/SharedPreferences)提取 Okta 存储的授权数据。

现在更改适配器的头，使它接受一个字符串形式的访问令牌。

```
class MovieAdapter(val context: Context, val token: String?) 
```

Enter fullscreen mode Exit fullscreen mode

然后当您在`MainActivity`中实例化它时，从 auth state 对象传入令牌。

```
adapter = MovieAdapter(this.baseContext, readAuthState().accessToken) 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要更改`MovieApiClient.kt`中电影的 get 调用，以包含一个授权头。

```
@GET("movies") fun getMovies(@Header("Authorization") token:String): Observable<MovieEmbedded> 
```

Enter fullscreen mode Exit fullscreen mode

回到适配器中，更改`refreshMovies()`以使用这个新的头——带有新的令牌和一个*载体*前缀:

```
fun refreshMovies() {
    client.getMovies("Bearer $token")
} 
```

Enter fullscreen mode Exit fullscreen mode

您需要对其他方法(添加、删除、更新)进行同样的更改，以便这些功能能够与您的经过身份验证的后端一起工作。

应该就是这样了。重新部署应用程序，你应该像以前一样通过一个列表-这一次它是经过验证的！

恭喜你！您刚刚完成了一个完整的客户机-服务器解决方案，它有一个健壮且兼容的 REST 后端，前端有一个使用最新技术的 Android 应用程序，所有这些都有集中的、行业标准的身份验证。你可以在 GitHub 上找到本教程[中创建的例子。](https://github.com/oktadeveloper/okta-android-kotlin-crud-example)

## 了解有关 Android、Java 和安全认证的更多信息

我们已经写了一些其他很酷的 Spring Boot 和安卓教程，如果你感兴趣的话可以看看。

*   [开始使用 Spring Boot、OAuth 2.0 和 Okta](https://developer.okta.com/blog/2017/03/21/spring-boot-oauth)
*   [与 Spring Boot 和 React 的良好发展](https://developer.okta.com/blog/2017/12/06/bootiful-development-with-spring-boot-and-react)
*   [创建一个没有 IDE 的基本 Android 应用](https://developer.okta.com/blog/2018/08/10/basic-android-without-an-ide)

最后，如果你想了解更多关于 Kotlin 的知识，一旦你使用了一段时间，一个很好的地方就是 Kotlin [习语页面](https://kotlinlang.org/docs/reference/idioms.html)。

如果您有任何问题，请不要犹豫在下面留下评论，或者在我们的 [Okta 开发者论坛](https://devforum.okta.com/)上询问我们。如果你想看更多像这样的教程，请在推特上关注我们 [@oktadev](https://twitter.com/oktadev) ！