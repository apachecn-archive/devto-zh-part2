# 一起使用 Python 和 C

> 原文：<https://dev.to/rapidnerd/using-python-and-c-together-d4e>

Python 和 C，这两种最流行和最伟大的语言都是从编程中诞生的。许多语言可以很容易地相互交流，但是用 Python 和 C 就有点棘手了。但首先让我们从为什么我们想要这个开始？

毫无疑问，这两种语言都非常强大，非常有用。在 Python 项目中使用 C 语言的原始性能有时是值得的，它肯定有助于减少响应和处理时间等过程。

# 我们需要什么

Python，还有 c .就是这样。

# 代码

在这个例子中，我将使用一个简单的斐波那契函数来演示这一切。

```
 #include <Python.h>  
// create the function like you normally would in C
int CFib(int n){
    if(n < 2)
        return n;
    else return CFib(n - 1) + CFib(n - 2)
}
// this function will be binding our python version and our C version together
// will only take one and only one non-keyword arguement
static PyObject* fib(PyObject* self, PyObject* args) {
    int n;
    if(!PyArg_ParseTuple(args, "i", &n))
        return NULL;
     return Py_BuildValue("i", CFib(n))
} 
```

Enter fullscreen mode Exit fullscreen mode

在代码中，我们可以看到我们需要`Python.h`头文件，它包含了所有相关的方法、函数、属性等，我们需要它们来让两种语言协同工作。我们首先在 C 中正常创建函数，然后使用 Python 头文件中的方法再次创建它，但是多了几个参数。

如你所见，我们在函数中使用了构建器和解析器。它们在两种语言之间进行通信，以创建 c 语言中函数的 python 版本。

此外，我们需要一个用 Python 写的小设置脚本

```
from distutils.core import setup, Extension

setup(name='ModuleName', version='1.0', ext_modules=[Extension('ModuleName', ['Fib.c'])]) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，大部分都是不言自明的。我们从 Python 3+内置的库导入，该库允许我们运行设置和扩展脚本，这两者都与 C 和 Python 头文件兼容

为了运行它并将其保存为我们自己的项目，我们需要运行这两个命令
`python setup.py build`
`python setup.py install`
这将允许你从任何其他 python 项目调用你的模块。

现在是大结局

```
import ModuleName # really should've chosen a better name ModuleName.CFib(2) 
```

Enter fullscreen mode Exit fullscreen mode

打印输出 1

这个在我的 GitHub 上有，如果你想叉
[点击我！](https://github.com/Eros/PyCExtensions)
此外，这是一个非常基础的版本，你可以在[的 Python 教程网站](https://docs.python.org/3/extending/index.html)上跟进如何更深入地学习

任何错误或建议请随时告诉我< 3