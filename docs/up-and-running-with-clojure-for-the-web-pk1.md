# 使用 clo jure for Web 启动和运行

> 原文：<https://dev.to/deciduously/up-and-running-with-clojure-for-the-web-pk1>

# 用 Boot 快速设置 Clojure webserver

## 开机

我已经尽了最大努力让这一点容易理解，即使你一生中从未见过 Clojure 的一行，但当然我不能谈论每一个方面。在开始之前，你可能想花点时间看看《勇敢和真实的 Clojure》的第三章:[做事](https://www.braveclojure.com/do-things/)。这是一个很好的语法速成班——真的没有太多的语法需要学习，你真的不需要太多来理解这篇文章或开始构建端点。

这本书(正确地)建议你跟随 T2 REPL。我最喜欢的快速 REPL 是[普朗克](http://planck-repl.org)，但是你可以使用这个项目中的工具通过抓取我下面讨论的 [Makefile](https://github.com/deciduously/example-com/blob/post1/Makefile) ，运行`make deps`，然后运行`bin/boot repl`来完成它。这需要一点时间，尤其是第一次。

请在[ben@deciduously.com](mailto:ben@deciduously.com)告诉我是否有什么需要改进的地方！

这个职位将涉及到建立一个空白项目。如果你想跟上进度，我强烈建议你自己打字，但是有一个例子[回购](https://github.com/deciduously/example-com)你可以复制。

### 依赖关系

如果你已经安装了 JDK、`bash`、GNU `make`和`curl`，你就可以开始了。如果你没有，你的操作系统/软件包经理可以帮你解决。真的是这样——我使用`tar`和`xz`来压缩发布，你可以使用任何你喜欢的东西。

如果你以前使用过 Leiningen，建立一个`build.boot`文件与建立一个`project.clj`文件非常相似。如果没有，不要担心。这很容易调整和测试。

### [T1】build . boot](#buildboot)

如果你只使用过`lein`，初始设置会稍有不同，但不是很大。首先，设置你的文件夹:

```
mkdir -p example-com/ && cd example-com/ && git init
echo "target/\n" > .gitignore
echo "v0.0.1" > version.properties 
```

Enter fullscreen mode Exit fullscreen mode

我还加了`.#\n.nrepl-`来过滤掉 emacs/cider 的东西，你要量体裁衣。

欢迎您以您喜欢的任何方式获得`boot`，并且最终可能想要全局安装它，但是您可以使用下面的 Makefile 提供一个`make deps`命令来将 boot 安装到项目位置。shim 非常小，只加载您指定的版本，默认情况下是最新的，并读取用户的 maven 存储库。你可以在这里下载 Makefile [或者复制到下面:](https://github.com/deciduously/example-com/blob/post1/Makefile) 

```
# Makefile .PHONY: help deps

SHELL       := /bin/bash
export PATH := bin:$(PATH)

help:
    @echo "Usage: make {deps|help}" 1>&2 && false

bin/boot:
    (mkdir -p bin/                                                                              && \
    curl -fsSLo bin/boot https://github.com/boot-clj/boot-bin/releases/download/latest/boot.sh  && \
    chmod 755 bin/boot)

deps: bin/boot 
```

Enter fullscreen mode Exit fullscreen mode

安装 boot 后，运行`boot -u > boot.properties`。使用该文件指定要加载的 shim 的版本。

在项目的根目录下发布`touch build.boot`，用你喜欢的[编辑器](http://spacemacs.org)打开。从`set-env!`开始:

```
;; build.boot  (set-env!  :source-paths  #{"src/"}  :dependencies  '[[org.clojure/clojure  "1.9.0"]  [hiccup  "1.0.5"  :scope  "test"]  [pandeiro/boot-http  "0.8.3"  :scope  "test"]]) 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，`:dependencies`向量被引用来传递给`set-env`，不像`defproject`，你使用一组:source-paths。当我们使用它们时，我会逐一检查图书馆。

指定`:scope "test"`确保这些 dep 保持[在 jar](https://www.zazzle.com/rlv/stay_out_hands_candy_cookie_jar_candy_jars-r7ec7cc8b404143a3be44e853c1d7e4ef_2ih7l_8byvr_512.jpg) 之外，只需要提供预编译的 html、css 和 javascript！

许可证/描述信息由`task-options` :
中的`pom`选项指定

```
(task-options!  pom  {:project  'example-com  :description  "Static website generator and server"  :url  "http://www.example.com"  :license  {"MIT"  "https://mit-license.org"}  :developers  {"you"  "dev@example.com"}})  (require  '[pandeiro.boot-http  :refer  [serve]]) 
```

Enter fullscreen mode Exit fullscreen mode

设置您喜欢的任何选项。唯一需要的是`:project`和`:version`，你可以通过在`(require)`表格后添加`(second (str/split (slurp "version.properties") #"="))`从`version.properties`获得。`#""`是一个 reader 宏，编译成 [`java.util.regex.Pattern`](https://docs.oracle.com/javase/9/docs/api/java/util/regex/Pattern.html) ，而`slurp`在指定的文件上打开一个 reader，返回一个包含内容的字符串。

我们还从 [`boot-http`](https://github.com/pandeiro/boot-http) 中拉入我们将要编写的`web`名称空间和`serve`任务。

现在可以添加一个开发任务:

```
(deftask  dev  "Run live development server"  []  (comp  (serve  :handler  'example-com.web/dev-handler  :reload  true  :port  3000)  (wait))) 
```

Enter fullscreen mode Exit fullscreen mode

就这样吧！四种形式。配置`boot`开始时非常简单。您可以用`comp`构建自己的构建管道——这些管道可读性很强，并按照您的预期运行。这个程序目前只包含了`serve`任务，但是很容易添加另一行。

现在，最后，让我们创建一个 Clojure 文件。执行`mkdir -p src/example_com/ && touch src/example_com/web.clj`，注意目录中的下划线代替了项目名称中的破折号。然后声明名称空间:

```
;; web.clj  (ns  example-com.web  (:require  [hiccup.core  :refer  [html]])) 
```

Enter fullscreen mode Exit fullscreen mode

我们只需引入`html`函数，将来自 [`hiccup`](https://github.com/weavejester/hiccup) 的 Clojure 向量编译成 html 字符串。

然后添加一个非常基本的[环处理程序](https://github.com/ring-clojure/ring/wiki/Concepts) :

```
(defn  dev-handler  [req]  {:status  200  :headers  {"Content-Type"  "text/html"}  :body  (html  [:h1  "Hello, world!"])}) 
```

Enter fullscreen mode Exit fullscreen mode

我特意将`core`留给了`server.jar` main 函数——您可以随意命名名称空间。

下面是`tree` :
的预期输出

```
.
├── bin
│   └── boot
├── boot.properties
├── build.boot
├── Makefile
├── src
│   └── example_com
│       └── web.clj
└── version.properties

3 directories, 7 files 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！运行`boot dev -h`以确保你没有得到任何错误，然后`boot dev`将在`localhost:3000`上运行一个服务器。第一次运行时要有耐心，因为它会收集对本地 maven repo 的依赖。

一旦完成，它将输出`Started Jetty on http://localhost:3000`。将您的浏览器指向那里，您应该会看到:

# 您好，世界！

如果不是，请仔细检查您的所有语法——您可以与这个[标记的提交](https://github.com/deciduously/example-com/releases/tag/post1)进行比较。最容易犯的错误是在项目源文件夹中用下划线替换破折号。

恭喜你！你建立了一个网络服务器。对 Clojure 文件进行编辑并重新加载您的浏览器，并验证更改是否被重新编译并即时提供。使用`C-c`停止服务器，去泡杯茶——这是你应得的。这是提交工作的好时机:`git commit -m "Initial commit"`。