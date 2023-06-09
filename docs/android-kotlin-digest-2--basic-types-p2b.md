# Android-Kotlin 文摘# 2–基本类型

> 原文：<https://dev.to/itscoderslife/android-kotlin-digest-2--basic-types-p2b>

我又回到了科特林。

Kotlin 运行在 Java 虚拟机(JVM)上。

声明变量的语法:

可变:变量名称:类型=值

不可变:值名称:类型=值

| 类型 | 位宽度 |
| --- | --- |
| 两倍 | Sixty-four |
| 浮动 | Thirty-two |
| 长的 | Sixty-four |
| （同 Internationalorganizations）国际组织 | Thirty-two |
| 短的 | Sixteen |
| 字节 | eight |

Kotlin 1.1 支持在数值中使用下划线(_)以提高可读性:

val mySalary = 1_000_000

选择信用卡帐户= 20，000

==和= = = =之间的差异:

val a : Int = 10

val b : Int = 10

(a == b) //给出 true

(a === b) //给出 true

注意:这里比较的是基本类型。

当基本类型可为空时，它被转换为引用，结果如下:

val a : **Int？** = 10

val b : **Int？** = 10

(a == b) //给出 true

(a === b) //给出**假**

注意:这里 **a** 和 **b** 成为参考，因此与 **===** 的比较结果为假。

类型转换器:

c '。toInt()

toString()

原始字符串由三重引号( **"""** )分隔，不包含转义，并且可以包含换行符和任何其他字符:

选择文本=”项

```
for (c in “foo”)

    print(c) 
```

Enter fullscreen mode Exit fullscreen mode

"""

您可以使用 **trimMargin()** 函数删除前导空格:

选择文本=”项

```
|Tell me and I forget.

|Teach me and I remember.

|Involve me and I learn.

|(Benjamin Franklin) 
```

Enter fullscreen mode Exit fullscreen mode

“‘t0’。‘trim margin()’t1

—快乐编码—