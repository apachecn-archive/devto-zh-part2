# Java 的 ArrayList.remove()的 int vs. Integer 陷阱

> 原文：<https://dev.to/awwsmm/the-int-vs-integer-pitfall-of-javas-arraylistremove-cpm>

当你试图使用 [ArrayList.remove()](https://docs.oracle.com/javase/7/docs/api/java/util/ArrayList.html#remove(int)) 删除特定的元素时要小心，使用装箱的`Integer`而不是原语`int`——你可能不会得到你期望的结果:

```
jshell> ArrayList alist = new ArrayList(Arrays.asList('a', 'b', 'c', 'd', 'e'))
alist ==> [a, b, c, d, e]

jshell> alist.remove((Integer)3)
$2 ==> false

jshell> alist
alist ==> [a, b, c, d, e] 
```

Enter fullscreen mode Exit fullscreen mode

...这是因为 ArrayList 有两个 remove()方法。[一个带一个`java.lang.Object`](https://docs.oracle.com/javase/7/docs/api/java/util/ArrayList.html#remove(java.lang.Object))[一个带一个本原`int`](https://docs.oracle.com/javase/7/docs/api/java/util/ArrayList.html#remove(int)) 。

```
jshell> alist.remove(3)
$4 ==> 'd'

jshell> alist
alist ==> [a, b, c, e] 
```

Enter fullscreen mode Exit fullscreen mode

当你使用前者时，Java 试图匹配你的列表中的`Integer` *对象*，而不是删除指定索引处的对象。在大多数情况下，这不会是你想要的。做以下事情时要特别小心:

```
jshell> ArrayList toDelete = new ArrayList(Arrays.asList(0, 2))
toDelete ==> [0, 2]

jshell> for (Integer ii : toDelete) alist.remove(ii)

jshell> alist
alist ==> [a, b, c, e] 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们在 jshell 中没有得到出错的提示(就像上面当`remove()`返回`false`时我们所做的)。确保总是循环原语`int` s:

```
jshell> for (int ii : toDelete) alist.remove(ii)

jshell> alist
alist ==> [b, c] 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章最初以稍微不同的形式出现在我的 Wordpress 博客上。