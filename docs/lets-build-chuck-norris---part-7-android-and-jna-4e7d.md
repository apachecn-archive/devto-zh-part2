# 让我们建造查克·诺里斯！——第七部分:安卓和 JNA

> 原文：<https://dev.to/dmerejkowsky/lets-build-chuck-norris---part-7-android-and-jna-4e7d>

*原载于[我的博客](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)T3。*

*注意:这是[的第 7 部分，让我们打造 Chuck Norris！](https://dmerej.info/blog/post/introducing-the-chuck-norris-project/)系列。*

[上次](https://dmerej.info/blog/post/chuck-norris-part-6-android-cross-compilation/)我们设法为 Android 交叉编译和运行 C++代码。

现在是时候写一些 Java 代码了，但是我们需要先在桌面上走一段弯路。

# Java 绑定

让我们用`gradle` :
创建一个新的 Java 库项目

```
$ cd chucknorris
$ mkdir java && cd java
$ gradle init --type java-library 
```

Enter fullscreen mode Exit fullscreen mode

Gradle 创建了一堆文件，但是现在我们只关心源代码和测试。

我们的目标是编写一个测试，证明我们确实可以获得一些 ChuckNorris 事实。

让我们从删除生成的`build.gradle` :
中的 cruft 开始

```
dependencies { - api 'org.apache.commons:commons-math3:3.6.1'
- implementation 'com.google.guava:guava:23.0' } 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们修复 gradle 创建的源文件，这样我们就有了合适的包:

```
$ tree java
├── build.gradle
├── gradle
└── src
    ├── main
    │   └── java
    │   └── com
    │   └── chucknorris
    │   └── ChuckNorris.java
    └── test
        └── java
            └── com
                └── chucknorris
                    └── ChuckNorrisTest.java 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以写一个失败的测试:

```
/* In ChuckNorrisTest.java */
public class ChuckNorrisTest {

    @Test
    public void testGetFact() {
        ChuckNorris ck = new ChuckNorris();
        String fact = ck.getFact();
        assertThat(fact, containsString("Chuck Norris"));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
/* In ChuckNorris.java */

public class ChuckNorris {
    String getFact() {
        return "";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们运行测试:

```
$ ./gradlew test
> Task :test FAILED

com.chucknorris.ChuckNorrisTest > testGetFact FAILED
    java.lang.AssertionError at ChuckNorrisTest.java:14

1 test completed, 1 failed

# open build/reports/tests/test/index.html

java.lang.AssertionError:
Expected: a string containing "Chuck Norris"
     but: was "" 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这失败是有原因的。

现在我们可以尝试使用 JNA 加载我们的共享库。

首先，我们在`build.gradle`文件中添加依赖关系:

```
dependencies { +   api 'net.java.dev.jna:jna:4.5.1'
    testImplementation 'junit:junit:4.12'
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们准备使用 JNA:

*   我们使用`Native.loadLibrary()`来加载共享库
*   我们创建了一个`CLibrary`接口，它实现了我们希望作为方法调用的 C 函数。(暂时只是`chuck_norris_init`)。
*   我们在 ChuckNorris 类的构造函数中调用`chuck_norris_init`，将结果存储到 JNA `Pointer`:

```
public class ChuckNorris {
  private Pointer ckPointer;

  private static CLibrary loadChuckNorrisLibrary() {
    return (CLibrary) Native.loadLibrary("chucknorris", CLibrary.class);
  }

  public interface CLibrary extends Library {
    CLibrary INSTANCE = loadChuckNorrisLibrary();

    void chuck_norris_init();
  }

  public ChuckNorris() {
    ckPointer = CLibrary.INSTANCE.chuck_norris_init();
  }

  public String getFact() {
    return "";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们运行测试:

```
$ ./gradlew test
java.lang.UnsatisfiedLinkError: Unable to load library 'chucknorris':
  Native library (linux-x86-64/libchucknorris.so)
  not found in resource path (...)
  at com.sun.jna.NativeLibrary.loadLibrary(NativeLibrary.java:303)
  at com.sun.jna.NativeLibrary.getInstance(NativeLibrary.java:427)
  ... 
```

Enter fullscreen mode Exit fullscreen mode

这是意料之中的。我们从未告诉 JNA`libchucknorris.so`文件在哪里。

提醒一下，该文件目前保存在`build/default`文件夹中。我们是这样构建的:

```
$ cd build/default
$ conan install ../..
$ cmake -GNinja -DBUILD_SHARED_LIBS=ON ../..
$ ninja 
```

Enter fullscreen mode Exit fullscreen mode

有几种方法可以告诉 JNA 共享库文件的位置。这里我们将在 Gradle 脚本的`test`块中设置一个系统属性:

```
def thisFile = new File(project.file('build.gradle').absolutePath)
def projectPath = thisFile.getParentFile()
def topPath = projectPath.getParentFile()
def cppPath = new File(topPath, "cpp")
def cppBuildPath = new File(cppPath, "build/default/lib")

test {
    systemProperty 'jna.library.path', cppBuildPath
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们重新运行测试，我们会得到第一个失败:

```
$ ./gradlew test

java.lang.AssertionError:
Expected: a string containing "Chuck Norris"
     but: was "" 
```

Enter fullscreen mode Exit fullscreen mode

但是我们成功实例化了`ChuckNorris`类，所以这是一个进步:)

让我们实现`getFact()`，并且当我们这样做的时候，添加一个`.close()`方法:

```
 public interface CLibrary extends Library {
    // ...
    void chuck_norris_init();
    String chuck_norris_get_fact(Pointer pointer);
    void chuck_norris_deinit(Pointer pointer);
  }

  public ChuckNorris() {
    ckPointer = CLibrary.INSTANCE.chuck_norris_init();
  }

  public String getFact() {
    return CLibrary.INSTANCE.chuck_norris_get_fact(ckPointer);
  }

  public void close() {
    CLibrary.INSTANCE.chuck_norris_deinit(ckPointer);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

重新运行测试:

```
$ ./gradlew test
BUILD SUCCESSFUL 
```

Enter fullscreen mode Exit fullscreen mode

成功！

# 创建新的 Android 项目

现在我们知道:

*   如何编译 Android 的 C++代码？
*   如何用 Java 加载一些 C++，但只针对桌面。

是时候把东西粘在一起了。

为此，最好的方法是使用 Android Studio 创建 gradle 项目，从一个基本活动开始，这样我们就不必处理所有的 Android 样板文件。

## 调整图形用户界面

让我们假设 ChuckNorris 类现在已经存在 <sup id="fnref1">[1](#fn1)</sup> 。

我们从在`content_main`布局中为文本视图添加一个`text_view` ID 开始。

然后我们修改`MainActivity.java`文件，在点击浮动按钮动作时更新文本视图:

```
public class MainActivity extends AppCompatActivity {
  private ChuckNorris chuckNorris;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    chuckNorris = new ChuckNorris();
    super.onCreate(savedInstanceState);
  }

  @Override
  protected void onDestroy() {
    chuckNorris.close();
    super.onDestroy();
  }

  // ...

  final TextView textView = (TextView) findViewById(R.id.text_view);
  FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
  fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
      String fact = chuckNorris.getFact();
      textView.setText(fact);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 添加 ChuckNorris 源

Java 的口号之一是“写一次，到处跑” <sup id="fnref2">[2](#fn2)</sup>

所以让我们:

*   将 JNA 添加到依赖项中
*   添加我们之前写的 ChuckNorris.java 文件

一切都应该正常，对吗？

## 与 jnidispatch.so 同乐

为了检查我们的代码是否有效，让我们创建一个模拟器，然后单击 play。

我们面临着:

> **查克诺里斯已经停止**
> 
> 再次打开应用程序

什么？查克·诺里斯*不能被阻止*，这是不可接受的！

[![Chuck Norris punching the screen](img/614d32ecce5af126348a7ea11598906f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CEq3U4kN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://dmerej.info/blog/pics/chuck-norris-punch.gif)

查看日志的时间:

```
06-18 14:27:18.553 6890-6890/info.dmerej.chucknorris E/AndroidRuntime:
FATAL EXCEPTION: main
Process: info.dmerej.chucknorris, PID: 6890
java.lang.UnsatisfiedLinkError:
Native library (com/sun/jna/android-x86-64/libjnidispatch.so)
not found in resource path (.)
  at com.sun.jna.Native.loadNativeDispatchLibraryFromClasspath
  at com.sun.jna.Native.loadNativeDispatchLibrary
  ...
  at info.dmerej.chucknorris.ChuckNorris.loadChuckNorrisLibrary 
```

Enter fullscreen mode Exit fullscreen mode

那是一个有趣的。原来依赖名*变*编译为 Android 时需要一个`@aar`前缀 <sup id="fnref3">[3](#fn3)</sup> :

```
dependencies {
  // ...
  implementation 'net.java.dev.jna:jna:4.5.1@aar'
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们再试一次！

我们得到同样的错误消息，但是这次是没有找到`chucknorris.so`库:

```
06-18 14:27:18.553 6890-6890/info.dmerej.chucknorris E/AndroidRuntime:
FATAL EXCEPTION: main
Process: info.dmerej.chucknorris, PID: 6890
java.lang.UnsatisfiedLinkError: Unable to load library 'chucknorris':
  Native library (android-x86-64/libchucknorris.so) not found 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是，有一个或多或少的标准解决方案。

如果您将一个`.so`文件放在一个名为`src/main/jniLibs/<arch>`的文件夹中，它将被包含在 Java 应用程序中，Java 代码无需任何配置就可以加载它。

## 共享选项

为了简单起见，我们将 ChuckNorris 库构建为静态库，只是为了表明 C++二进制文件仍然需要`libc++_shared.so`才能运行。但是 JNA 需要一个共享库来运行。

记得在[第 4 部分](https://dmerej.info/blog/post/chuck-norris-part-4-python-ctypes/)中，我们必须用`-DBUILD_SHARED_LIBS=ON`调用 CMake 来获得一个共享库。

我们会做同样的事情，但这次是通过柯南。

首先，让我们在`android`配置文件中添加`ChuckNorris:shared`选项:

```
...
[options]
*:pic=True
ChuckNorris:shared=True 
```

Enter fullscreen mode Exit fullscreen mode

然后修改食谱:

```
class ChucknorrisConan(ConanFile):
    name = "ChuckNorris"
    ...
    options = {"shared": [True, False]}
    default_options = "shared=False"

    def build(self):
        cmake = CMake(self)
        cmake_definitions = {}
        if self.options.shared:
            cmake_definitions["BUILD_SHARED_LIBS"] = "ON"
        cmake.configure(defs=cmake_definitions)

    def package(self):
        self.copy("lib/libchucknorris.so", dst="lib", keep_path=False)
        self.copy("lib/libc++_shared.so", dst="lib", keep_path=False) 
```

Enter fullscreen mode Exit fullscreen mode

那我们就重新制作柯南包:

```
$ conan create . dmerej/test --profile android --setting arch=x86_64
Exporting package recipe
...
package(): Copied 2 '.so' files: libchucknorris.so, libc++_shared.so
Package '<hash>' created 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们创建包中所有`.so`文件的符号链接。

```
$ cd android/app
$ cd src/main
$ mkdir -p jniLibs/x86_64
$ cd jniLibs/x86_64
$ ln -s ~/.conan/data/ChuckNorris/0.1/dmerej/test/<hash>/libchucknorris.so .
$ ln -s ~/.conan/data/ChuckNorris/0.1/dmerej/test/<hash>/libc++_shared.so . 
```

Enter fullscreen mode Exit fullscreen mode

让我们再试一次:

[![Chuck Norris app running](img/1dccc69f482d8049e3aac44e19d9c21e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g-KjSXiV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/chuck-norris-android.png)

胜利\o/

今天到此为止。下次见！

* * *

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。

* * *

1.  这也被称为[一厢情愿的编程](https://dev.to/_bigblind/quick-tip-programming-by-wishful-thinking-3hn)T2】↩

2.  和往常一样，[维基百科页面](https://en.wikipedia.org/wiki/Write_once,_run_anywhere)包含了许多关于这个话题的有趣内容。 [↩](#fnref2)

3.  你可以在 [JNA 的 FAQ](https://github.com/java-native-access/jna/blob/master/www/FrequentlyAskedQuestions.md#jna-on-android) 中找到关于这个的注释，但是据我所知，在文档中没有*的其他地方*。 [↩](#fnref3)