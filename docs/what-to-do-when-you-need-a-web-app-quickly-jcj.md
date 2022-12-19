# 当你急需一个网络应用程序时该怎么办

> 原文：<https://dev.to/hmlon/what-to-do-when-you-need-a-web-app-quickly-jcj>

最近，我需要一个软件，你可以输入一些数字，验证和处理它们，并打印一些结果。因为我需要用它做科学计算，我决定用 Python 和 [NumPy](http://www.numpy.org/) 和 [SciPy](https://www.scipy.org/) 一起使用，因为我需要验证，我决定 HMTL 输入验证将是最容易使用的。

然后我从大量的 Python web 框架中做出选择，因为“微框架”听起来正是我所需要的，在我决定尝试使用 [Flask](http://flask.pocoo.org/) 之前，我听说过这个名字。

## 入门

安装完 Flask 之后，你只需要一个 python 文件。

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def index():
    return "Hello there!" 
```

用一个简单的命令启动它:

```
FLASK_APP=app.py FLASK_ENV=development flask run 
```

这就是在“/”页面上呈现一个简单字符串所需的全部内容。但是谁他妈的需要渲染一个字符串呢？即使你所需要的只是呈现一个字符串，如果没有一些 CSS，它看起来会很糟糕。所以我们将深入探讨渲染模板。

## 渲染模板

如果你想渲染一个模板，你可以使用一个名为`render_template`的内置方法。

```
from flask import Flask, render_template
app = Flask(__name__)

@app.route("/")
def index():
    return render_template('index.html') 
```

您还需要在`/templates`文件夹中创建`index.html`页面。

```
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  Computer math algorithms
</head>
<body>
  <h1>Computer math algorithms</h1>
  <ul>
    <li><a href="/equations">System of linear equations</a></li>
  </ul>
</body>
</html> 
```

如你所见，我已经添加了一个到`/equations`的链接，但是如果你现在点击这个链接，你将会看到“404 未找到”页面。要解决这个问题，你需要添加另一条路线和另一个模板，就像我们之前做的一样。

```
@app.route("/equations")
def equations():
    return render_template('equations.html') 
```

## 第一种形式

目前的项目是要解决一个线性方程组。首先，我们需要知道有多少个方程。为了得到这个数字，我们可以使用一个只有一个数字输入的表单。

```
<form action="/equations">
  <label for="equations_number">Number of equations:</label>
  <input type="number" name="equations_number" required min="1" max="100">
  <br>
  <input type="submit" value="Submit">
</form> 
```

顺便说一下，对于一些简单的 CSS，我推荐阅读[一个伟大的网站](http://bettermotherfuckingwebsite.com/)。要找到那里使用的 CSS，你可以使用开发者工具。

[![](../Images/ad976fee16368b90eade808ab6caefb8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GK12cTa8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2728/1%2AdrvZxl2vP8LENJ3Q1Z5Ucw.png)

由于输入标签上的必需属性，该表单不能在没有方程数的情况下提交。此外，由于`min`和`max`属性的缘故，该数字不能小于 1，也不能大于 100。

这个数字可能会大于 100，但网站将无法使用(10 之后就不再可用了)。在这种情况下，我会添加从某种电子表格文件导入。

输入任何有效数字后，点击“提交”按钮，一个参数将被添加到 URL `/equations?equations_number=2`中。

## 处理争论

要读取后端参数，`request.args`可以这样使用:

```
from flask import request

@app.route("/equations")
def equations():
    equations_number = request.args.get('equations_number', type = int)
    return render_template('equations.html', equations_number=equations_number) 
```

这里，我们将所需参数的名称和类型传递给 request.args.get，以便将其转换为 integer。然后将 equations_number 参数传递给 render_template，以便它在模板中可用。

## 这是线性方程组吗？

对于方程组，我们所需要的是一个系数矩阵`A`和一个列向量`b`，这样方程就是`Ax=b`。示例:

```
x + 2y = 3
4x + 5y = 6 
```

在这种情况下，我们会有下面的`A`和`b` :

```
A = [[1, 2], [4, 5]]
b = [[3], [6]] 
```

## 用二维数组输入制作表格

现在我们需要一个用于`A`和`b`的表单。

```
<form action="/equations">
  {% for i in range(equations_number) %}
    {% for j in range(equations_number) %}
      <label for="a">A[{{i+1}}, {{j+1}}]:</label>
      <input type="number" name="a" class="matrix-number matrix-number--a" required>
    {% endfor %}
    <span class="b">
      <label for="b">b[{{i}}]:</label>
      <input type="number" name="b" class="matrix-number" required>
    </span>
    <br>
  {% endfor %}
  <input type="submit" value="Solve">
</form> 
```

这段代码所做的是创建一个表单，然后`equations_number` times 为每个等式呈现输入。并且每个方程需要`equations_number`系数(这就是带有`j`的 for 循环的目的)和等号(`b`)后的一个数字。

这里需要注意的另一件有趣的事情是，带有`name="a"`的输入被渲染了`equations_number^2`次，而对于`name="b"`则被渲染了【equations_number】次。Flask 支持这种类型的表单并从中读取数据`request.args.getlist`可以这样使用:

```
b = request.args.getlist('b', type=int) 
```

我们可以对`A`数组做同样的事情，但是我们需要它是二维的。这就是 NumPy 使用`reshape`方法的方便之处。你只需要提供一个数组和一个你想要的形状的元组。

```
import numpy as np
shape = (len(b), len(b))
A = request.args.getlist(‘a’, type=int) # 1-dimensional A = np.reshape(A, shape)                # 2-dimensional 
```

## 条件渲染

我想在提交后隐藏方程式数字表单。为此，我们可以使用“条件渲染”。很简单:

```
{% if equations_number %}
  <form action="/equations">... form for `A` and `b` ...</form>
{% else %}
  <form action="/equations">... form for `equations_number` ...</form>
{% endif %} 
```

[![](../Images/6cb0f732ed0f938db0f7d251c623485f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_hdrmIz6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A3MdaFhyw5-d7ogP6ia0Fwg.png)

## 最终求解系统

一旦我们有了所有的系数，我们就能解出方程组。我可以试着考虑如何对矩阵`A`求逆，然后乘以向量`b`，但是我不会。今天我们将再次使用 NumPy。这个库有一个我们想要的功能——解一个线性方程组，它叫做 [`numpy.linalg.solve`](https://docs.scipy.org/doc/numpy-1.15.0/reference/generated/numpy.linalg.solve.html) 。它所需要的只是我们已经拥有的母体`A`和`b`。

```
x = np.linalg.solve(A, b) 
```

这就是事情变得有点棘手的地方。因为我们用这一个动作来做所有与求解系统相关的事情，所以我们添加了一些 if。下面是最终代码:

```
equations_number = request.args.get('equations_number', type = int)
b = request.args.getlist('b', type=int)
A = np.array(request.args.getlist('a', type=int)).reshape(len(b), len(b))
x = np.array([])
if len(b) > 0:
    x = np.linalg.solve(A, b)
return render_template('equations.html', equations_number=equations_number, A=A, b=b, x=x) 
```

让我们用我们使用此操作的一些屏幕来检查一下:

*   Start: `equations_number`为`None`，其他变量均为空数组

*   输入方程的数目:`equations_number`存在，但所有其他变量仍然是空数组

*   输入 a 和 b:`equations_number`又是`None`,`b`是数组，`A`是 2D 数组，`x`是方程组的答案

这样我们就可以渲染出我们的最终形式:

```
{% if x.size > 0 %}
  <p>A = {{ A }}</p>
  <p>B = {{ b }}</p>
  <p>x = {{ x }}</p>
{% elif equations_number %}
  <form action="/equations">
    {% for i in range(equations_number) %}
      {% for j in range(equations_number) %}
        <label for="a">A[{{i+1}}, {{j+1}}]:</label>
        <input type="number" name="a" class="matrix-number matrix-number--a" required>
      {% endfor %}
      <span class="b">
        <label for="b">b[{{i}}]:</label>
        <input type="number" name="b" class="matrix-number" required>
      </span>
      <br>
    {% endfor %}
    <input type="submit" value="Solve">
  </form>
{% else %}
  <form action="/equations">
    <label for="equations_number">Number of equations:</label>
    <input type="number" name="equations_number" required min="1" max="100">
    <br>
    <input type="submit" value="Submit">
  </form>
{% endif %} 
```

[![](../Images/54de24d538e5584cae33df3c487a800b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9pVy0cvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2688/1%2A35qefjzGF59MzHkWWcrAPQ.png)

## 需要改进的地方(作业？)

*   在不同的屏幕上显示不同的视图和操作

*   [一个基地布局所有视图](http://flask.pocoo.org/docs/1.0/tutorial/templates/#the-base-layout)

*   更大的输入和更漂亮的输出的形式可访问性

## 结论

在我报道的这个故事中:

*   设置您的烧瓶应用程序

*   渲染模板

*   制作 HTML 表单

*   如何用 Flask 读取参数(常规，数组，甚至二维数组)

*   条件渲染

*   如何用 NumPy 表示和求解线性方程组

你可以在我的 [GitHub 页面](https://github.com/hmlON)上找到[这个项目的完整代码](https://github.com/hmlON/KPI/tree/master/Computer%20math%20algorithms)，以及我的其他项目。如果你喜欢这篇文章，你可以关注我，如果你不喜欢，你可以在下面留下愤怒的评论。

这篇文章最初发表在我的博客上。