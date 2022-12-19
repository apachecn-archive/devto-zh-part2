# “这个”是什么？

> 原文：<https://dev.to/basedenergy/what-is-this-4onl>

## 简化了**【本】** Javascript 关键字

“this”关键字总是引用一个**对象**，但是“this”的值根据它被调用的位置而变化。当调用一个函数时，会创建一个包含对“this”的引用的执行上下文。因为“this”的值根据执行上下文而变化，所以我们可以通过检查函数的**调用点**，或者它被调用的位置来确定“this”的值。

#### 方便的说，有四个简单的规则可以帮助确定“这个”的值！

### 规则 1:默认绑定

当单独调用或在函数中调用时，“this”将引用全局对象。在浏览器中，这指的是窗口对象。一个例外是如果用户处于严格模式，在这种情况下“this”将是未定义的。

[![default example](../Images/0cbbc746d80efd67001c345e50e39bb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9P-GUeWs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6voq05f3flwlffnevpcx.png) 

<figure>

<figcaption>在这个例子中，你会得到一个全局对象</figcaption>

</figure>

的控制台日志

### 规则二:隐式绑定

如果一个函数包含在一个对象中，“this”将指最近的父对象。即使函数只在对象中被引用，“this”还是会引用对象。

<figure>

[![implicit example](../Images/4c43c7896ef47341215395a62f001f42.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--23DH5nnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pb52nfl8kd4xaqpfkzqs.png)

<figcaption>In this example, we console log out "😸 Meow!"</figcaption>

</figure>

### 规则 3:显式绑定

有三个函数*调用*、*绑定*、*应用*可以用来设置“this”的值。调用和应用工作的方式基本相同，传递给每个对象的第一个参数是“this”指的是什么。Bind 实际上创建了一个新的函数，它被永久赋值为“this”。

<figure>

[![explicit example](../Images/15c4a2a1a9cbc65c4822d536e9357cd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rt3RvPDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khyr03oy7d5sh0rbtuta.png)

<figcaption>In this example, all three functions set "this" explicitly.</figcaption>

</figure>

### 规则 4:新绑定

当使用 **new** 关键字时，“this”将指正在创建的新对象。

<figure>

[![new example](../Images/f9be463a4353cb34fdda52680736ee80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VdzxZ2wM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3e3ugipckv0o1nrnt35.png)

<figcaption>In this example, "this" points to the new object and prints out my kitty's name</figcaption>

</figure>

### 箭头功能异常！

这些一般经验法则的一个例外是，当使用箭头函数时，“this”将保留其父作用域的值。

<figure>

[![arrow example](../Images/2b6d4382ba0027527d640a72b6bacd56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3X3JqWSD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t7mnz19w1emt6ljcxk81.png)

<figcaption>Here, "this" retains the value of its parent function.</figcaption>

</figure>

### 开发回顾:

所以快速总结一下“这个”关键词:

1.  “这”通常由函数的执行上下文决定。
2.  默认情况下，“this”指的是全局对象(浏览器中的窗口对象)。
3.  当使用“new”关键字时，“this”将指新对象。
4.  *调用*、*绑定*、*应用*可用于设置“此”值。
5.  箭头函数不会重新绑定“this”。

希望这对那些尝试理解“this”关键字的 Javascript 新手有所帮助！感谢您的阅读，不要忘记**关注**！！