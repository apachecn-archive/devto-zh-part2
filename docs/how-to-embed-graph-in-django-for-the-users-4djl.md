# 如何在 Django 中为用户嵌入图形

> 原文：<https://dev.to/jaydeepborkar/how-to-embed-graph-in-django-for-the-users-4djl>

我想向用户显示一个图表(使用 matplotlib)。我应该把代码放在哪个 Django 目录下？

mysite/

graph/
my _ matplotlib/
**init**。py
my _ matplotlib . py
graph code . py(基本上是图形的代码)

app/
views.py
从 my_matplotlib.my_matplotlib 导入 my_func

my_matplotlib.py

def my_func():
foo = 'bar'
返回 foo

我试过这个，但是不起作用。