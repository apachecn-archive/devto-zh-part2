# 安卓-科特林文摘#1

> 原文：<https://dev.to/itscoderslife/android-kotlin-digest-1-4nn7>

计划记录我在 Android 和 Kotlin 开发方面的学习进展。这主要是从一个 iOS 开发者的角度来看。我会写下我今天所学的要点和任何特别有趣的东西。

下面是我在 Kotlin 学到的一些东西以及与 Swift 的不同之处。

科特林的一些基本知识:

`var`可变变量

`val`不可变的变量/常数

在字符串中添加变量值`"The value of variable is $variable_name"`

后缀`?`用于保存空值的变量

也有一个 javascript 大箭头`=>`功能类似语法:

`fun sum(a: Int, b: Int) : Int = a + b`

条件句:`if (obj is String) { … }`和非条件句`if (obj !is String) { … }`

我发现迄今为止最独特的陈述是:

 `when (obj) {
1 -> "One" // (case)
"Hello" -> "Greeting" // (case)
is Long -> "Long" // (case)
!is String -> "Not a string" // (case)
else -> "Unknown" // (default case)
}` 

范围被指定为`0..end`或`0..end+1`或`0..list.lastIndex`

然后我们有进步:

`for (x in 1..10 step 2) {
print(x)
}` 用关键字`step`和值 2 跳转交替步骤。这里的结果将是:`13579`

`for (x in 9 downTo 0 step 3) {
print(x)
}`

跳三步但与关键字`downTo`顺序相反。这里的结果将是:`9630`