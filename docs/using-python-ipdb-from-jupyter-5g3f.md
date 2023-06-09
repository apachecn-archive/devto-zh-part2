# 使用 Jupyter 的 Python ipdb

> 原文：<https://dev.to/andreagrandi/using-python-ipdb-from-jupyter-5g3f>

*(此博文原载于我的博客[https://www . Andrea grandi . it/2016/05/10/using-python-ipdb-from-jupyter/](https://www.andreagrandi.it/2016/05/10/using-python-ipdb-from-jupyter/))*

如果我们尝试从 Jupyter (IPython 笔记本)使用常用的 ipdb 命令

```
import ipdb; ipdb.set_trace() 
```

Enter fullscreen mode Exit fullscreen mode

我们会得到类似的错误:

```
-------------------------------------------------------------------------------
MultipleInstanceError                     Traceback (most recent call last)
<ipython-input-1-f2b356251c56> in <module>()
    1 a=4
---------> 2 import ipdb; ipdb.set_trace()
    3 b=5
    4 print a
    5 print b

/home/nnn/anaconda/lib/python2.7/site-packages/ipdb/__init__.py in <module>()
    14 # You should have received a copy of the GNU General Public License along with this program. If not, see http://www.gnu.org/licenses/.
    15 
--------> 16 from ipdb.__main__ import set_trace, post_mortem, pm, run, runcall, runeval, launch_ipdb_on_exception
    17 
    18 pm                       # please pyflakes

/home/nnn/anaconda/lib/python2.7/site-packages/ipdb/__main__.py in <module>()
    71         # the instance method will create a new one without loading the config.
    72         # i.e: if we are in an embed instance we do not want to load the config.
--------> 73         ipapp = TerminalIPythonApp.instance()
    74         shell = get_ipython()
    75         def_colors = shell.colors

/home/nnn/anaconda/lib/python2.7/site-packages/traitlets/config/configurable.pyc in instance(cls, *args, **kwargs)
    413             raise MultipleInstanceError(
    414                 'Multiple incompatible subclass instances of '
--> 415                 '%s are being created.' % cls.__name__
    416             )
    417

MultipleInstanceError: Multiple incompatible subclass instances of TerminalIPythonApp are being created. 
```

Enter fullscreen mode Exit fullscreen mode

解决方法是使用 Tracer 代替:

```
from IPython.core.debugger import Tracer
Tracer()() 
```

Enter fullscreen mode Exit fullscreen mode

来源:[http://stack overflow . com/questions/35613249/using-ipdb-to-debug-python-code-in-one-cell-jupyter-or-ipython](http://stackoverflow.com/questions/35613249/using-ipdb-to-debug-python-code-in-one-cell-jupyter-or-ipython)