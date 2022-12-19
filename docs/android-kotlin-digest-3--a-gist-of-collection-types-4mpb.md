# Android-kot Lin Digest # 3–集合类型的要点

> 原文：<https://dev.to/itscoderslife/android-kotlin-digest-3--a-gist-of-collection-types-4mpb>

今天在 Kotlin，学习了*数组，Hashmaps，ArrayList，MutableList，MapOf，ArrayOf* 。这是事情的要点。

在 **Swift** 中，我们将 Array 的所有功能都放在了一起。

在这里 **Kotlin** 我们可能需要使用*数组*和*数组列表*的组合。

以下是几个科特林的例子:

**数组**:

`var myArray = Array<Int>(6){2}
for (element in myArray) {
println("Item - " + element)
}`

对于(0 中的索引..myArray . size-1){

println(myArray[index])

}

**阵列列表**:

`var myArrayList = ArrayList<String>()
myArrayList.add("It's")
myArrayList.add("Coders")
myArrayList.add("Life")`

if(myarraylist . contains(" Coders ")

println("他是个摇滚明星！")

myarraylist . remove(" Coders ")

myarraylist . add(" Dams ")

//遍历对象

(my ArrayList 中的姓名){

println("姓名:"+姓名)

}

**散列表**:(Swift 中的字典)

`var hashmap = HashMap<String, String>()
hashmap.put("Mary", "Married")
hashmap.put("Paulo", "Married")
hashmap.put("John", "Single")`

for(k in hashmap . keys)

println(hashmap . get(k))

**其他收集工具:**

`var myArrayList = listOf<String>("Me", "James", "Bonni", "Life")
var mutableList = mutableListOf<String>("Me", "They", "James")
var myHashmap = hashMapOf(1 to "Paulo", 2 to "James")`

happy coding()；