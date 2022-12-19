# 用 Python 和 HTTP 服务你当前的目录

> 原文：<https://dev.to/nicolasmesa/serve-your-current-directory-with-python-and-http-2m3p>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

这将是一个简短的帖子，展示如何运行一个 HTTP 服务器来服务你当前的工作目录。

## TLDR

对于 Python 3 运行:

```
python3 -m http.server 
```

对于 Python 2 运行:

```
python -m SimpleHTTPServer 
```

`.bashrc`别名:

```
alias serve="python3 -m http.server" 
```

## 说明

有时在工作中，我需要启动一个快速 HTTP 服务器来服务于我当前的工作目录。我通常在两种情况下需要它:

1.  共享一个快速链接，让人们从我的电脑上下载一些内容。
2.  我在另一台电脑上有一个`index.html`文件和一堆静态资源，我不想复制整个文件夹才能在我的浏览器上看到这个页面。当我在另一台计算机上运行单元测试，并且需要查看覆盖率报告(以 HTML 呈现)时，这个用例经常出现。

事实证明，用 HTTP 服务器为当前目录提供服务很容易。你只需要`python`。

## 设置

在开始之前，让我们创建一些示例文件。

```
nmesa@desktop-nicolas:~/demos/serve-cwd$ echo file1 > file1.txt
nmesa@desktop-nicolas:~/demos/serve-cwd$ echo file2 > file2.txt
nmesa@desktop-nicolas:~/demos/serve-cwd$ mkdir dir1 dir2
nmesa@desktop-nicolas:~/demos/serve-cwd$ echo file3 > dir1/file3.txt
nmesa@desktop-nicolas:~/demos/serve-cwd$ echo file4 > dir2/file4.txt
nmesa@desktop-nicolas:~/demos/serve-cwd$ tree
. ├── dir1
│   └── file3.txt
├── dir2
│   └── file4.txt
├── file1.txt
└── file2.txt

2 directories, 4 files 
```

我们在根目录下创建了两个文件和两个目录，每个目录中有一个文件。

## 找出您的 python 版本

启动 HTTP 服务器的命令因 python 的版本而异。运行`python --version`以获得当前版本的`python`。

```
nmesa@desktop-nicolas:~/demos/serve-cwd$ python --version
Python 3.5.2 
```

如果看到`Python 3.x.x`，使用 Python 3 命令。如果看到`Python 2.x.x`，使用 Python 2 命令。

在这种情况下，我使用的是 Python 3。

## Python 3 命令

```
nmesa@desktop-nicolas:~/demos/serve-cwd$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 ... 
```

## Python 2 命令

```
nmesa@desktop-nicolas:~/demos/serve-cwd$ python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ... 
```

## 测试一下

前面的命令启动了一个监听端口`8000`的 HTTP 服务器。让我们将 web 浏览器指向端口`8000`(本例中为`192.168.0.250:8000`)上的服务器 IP 地址。该页面应该如下所示:

### 根目录

[![Sample root directory listing](../Images/2777e79655db39b179fbf0eeb4261f21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LHO_Y7sX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.nicolasmesa.co/posts/2018/09/sample_root_directory_listing.png) 示例根目录清单。这相当于在服务器运行的目录上运行一个`ls`。

### 子目录

让我们点击`dir1/`链接:

[![dir1 directory listing](../Images/2636b7ce31a7dbf39a412d8bfe3440ba.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--CvR7BV6f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.nicolasmesa.co/posts/2018/09/dir1_directory_listing.png) `dir1`目录清单。

### 文件

让我们点击`file3.txt`链接。

[![file3.txt contents](../Images/c57dfbb959e859acd56dab43ea63cdf6.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--0CV5DSuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.nicolasmesa.co/posts/2018/09/file3_file_contents.png) `file3.txt`目录。请注意，该文件呈现在浏览器中。这是因为 HTTP 服务器足够聪明，可以向 HTTP 响应添加内容类型。当您需要将 HTML 与 CSS、JS 和图像一起提供时，这变得非常方便。

## 更新(2018 年 10 月 21 日)

我在工作中经常使用它来提供测试覆盖报告。为了节省一些输入，让我们将下面的别名添加到我们的`.bashrc`(Mac 中的`.bash_profile`)文件中:

```
alias serve="python3 -m http.server" 
```

让我们通过从我们的目标目录运行`serve`来测试它(注意，您将需要运行`source ~/.bashrc`或启动一个新的终端来工作):

```
nmesa@desktop-nicolas:~/demos/serve-cwd$ serve
Serving HTTP on 0.0.0.0 port 8000 ... 
```

我们还可以指定不同的端口:

```
nmesa@desktop-nicolas:~/demos/serve-cwd$ serve 9000
Serving HTTP on 0.0.0.0 port 9000 ... 
```

该命令启动端口`9000`中的 web 服务器。

## 结论

Python 使得启动 HTTP 服务器来共享您当前的工作目录变得很容易。请不要忘记在你完成后关掉服务器，并且小心你的分享。

## 链接/延伸阅读

*   [原帖](https://blog.nicolasmesa.co/posts/2018/09/serve-your-current-directory-with-python-and-http/)
*   [SimpleHTTPRequestHandler 源代码(python 3)](https://github.com/python/cpython/blob/3.7/Lib/http/server.py#L627)
*   [SimpleHTTPRequestHandler 源代码(python 2)](https://github.com/python/cpython/blob/2.7/Lib/SimpleHTTPServer.py#L28)
*   [别名命令](http://www.linfo.org/alias.html)