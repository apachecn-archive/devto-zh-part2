# 将 Android 中的 ArrayList <string>()转换为 sting[]数组</string>

> 原文：<https://dev.to/sandeepkamboj12/convert-arrayliststring-to-stirng-array-in-android--3g02>

ArrayList list = new ArrayList<>()；
list . add(" A ")；
list . add(" B ")；
list . add(" C ")；
list . add(" D ")；

string[]string ar = textutils . join("，"，list)。拆分(“，”)；