# Building Python/Node.js development environment based on Visual Studio in Windows

> 原文：<https://dev.to/leon0824/-visual-studio--22h>

I haven't started Windows for a long time. Recently, when I encountered the reason why I had to start Windows, I took advantage of the opportunity to reinstall Windows and the development environment of Python and Node.js on the new SSD.

In particular, this time I want to build a development environment based on Git, Python and C++ Build Tools included in Visual Studio. Let's see if it can be successfully completed.

Of course, the starting method is to install the fat Visual Studio 2019 first, install all three workloads: Python Development, Node.js Development and Desktop Development with C++, and then make a cup of tea and wait for it.

[![Viiual Studio](../Images/56a121f8ce760aa35f9f6b86914b4dd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zynh7nn7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/t4e8kswdug3958s0m36l.png)

## 去吧

Although the installer didn't specifically inform you, Git is actually a part of the standard installation of Visual Studio, located at C: \ Program Files (x86) \ Microsoft Visual Studio \ 2019 \ Community \ Common 7 \ IDE \ Common Extensions \ Microsoft \ Team Foundation \ Team Explorer \ Git \ Git.exe is a smelly and long Path. You can add this git.exe to the path of the environment variable, which will be more convenient later.

By the way, Microsoft's own version control tool Team Foundation Version Control seems to have been abandoned, right?

## 巨蟒

Because "Python Development" was chosen when installing Visual Studio, Python will be installed along with it intimately. The location at C: \ Program Files (x86) \ Microsoft Visual Studio \ shared \ Python37 _ 64 \ python.exe is also smelly and long. Python's suite management tool pip is at pip/pip.exe in the same location. The same can be added to Path for easy use.

This Python installation is a system-wide installation. I'm not sure whether it will bring some potential disadvantages. I need to make a disclaimer.

Editor: For those who seriously want to build a Python environment, please see " [Building a Python 3 Development Environment](https://editor.leonh.space/2021/python/) ".

## Node.js

Unlike the first two, although "Node.js development" is also checked in Visual Studio installation, [T0】 Visual Studio Installer didn't help us install Node.js 【T1], so please go to the Node.js website to download and install Node.js yourself.

With regard to Node.js, if we consider that some npm suites are C++ suites in the future, npm will help us compile them, but we need to prepare a compilation tool for them in advance, that is, the MSVC C++ build tool, which is called " **MSVC V142–VS 2019C++x86/64 build tool (v14.25) 【T1] in Visual Studio 2019\. ), which is one of the components of Visual Studio Install Desktop Development with C++.**

After tossing, you can try to install the npm suite that needs to be compiled:

```
>  npm  install  -g  sqlite3 
```

Enter fullscreen mode Exit fullscreen mode

If there is no problem, there is no problem. If there is a problem, it cannot be called no problem.

## Conclusion

At this point, we have the development environment of Git, Python, Node.js and C++ in our computer. Next, install a Visual Studio code to write code happily! Visual Studio just lies quietly and starts to install B.