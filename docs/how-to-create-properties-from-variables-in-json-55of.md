# 如何在 JSON 中从变量创建属性

> 原文：<https://dev.to/equiman/how-to-create-properties-from-variables-in-json-55of>

JSON 和 JavaScript 彼此相爱……它们之间的交互是完全自然的，你可以从头开始动态地创建一个对象。

有时，我们需要从变量而不是从固定值创建 JSON 及其属性，这些属性可以在将来用作某种索引，并且将更容易获得信息，而不需要循环。

这真的很简单，我们可以创建一个对象，从变量中获取属性，然后给它们赋值:

`// hidden setup JavaScript code goes in this preamble area` `var json_list = {}; // Create an object as JSON root var user = 'foo'; // Create an 'user' variable and assign it 'foo' as property name json_list[user] = {}; // Creates new 'foo' property as object json_list[user].alert = {}; // Creates an 'alert' property as object in 'foo' property json_list[user].alert.time = 30; // You can assign a value using a variable ... json_list.foo.alert.type = 'sec'; // ... or directly with the property name user = 'bar'; // Use the same 'user' variable but assign it a new 'bar' value json_list[user] = {}; // Creates new 'bar' property as object var objAlert = { 'type': 'sec', 'time': 60 }; // Create an 'alert' objet filled with properties json_list[user].alert = objAlert; // Assign an 'alert' property as object from 'objAlert' variable json_list[user].alert.type = 'min'; // You can change his value using a variable... json_list.bar.alert.time = 80; // ... or directly with the property name // Convert JSON object to string var jsonstr = JSON.stringify(json_list);`

要查看转换成字符串的所有对象，只需在控制台中使用:

`// hidden setup JavaScript code goes in this preamble area var json_list = {}; var user = 'foo'; json_list[user] = {}; json_list[user].alert = {}; json_list[user].alert.time = 30; json_list.foo.alert.type = 'sec'; user = 'bar'; json_list[user] = {}; var objAlert = { 'type': 'sec', time: 60 }; json_list[user].alert = objAlert; json_list[user].alert.type = 'min'; json_list.bar.alert.time = 80; var jsonstr = JSON.stringify(json_list);` `// Click [RUN] button to see result console.log(jsonstr)`

现在开始我最喜欢的部分…访问信息没有循环。

`// hidden setup JavaScript code goes in this preamble area var json_list = {}; var user = 'foo'; json_list[user] = {}; json_list[user].alert = {}; json_list[user].alert.time = 30; json_list.foo.alert.type = 'sec'; user = 'bar'; json_list[user] = {}; var objAlert = { 'type': 'sec', time: 60 }; json_list[user].alert = objAlert; json_list[user].alert.type = 'min'; json_list.bar.alert.time = 80; var jsonstr = JSON.stringify(json_list);` `// Click [RUN] button to see result console.log(JSON.stringify(json_list["foo"])); console.log(json_list["foo"]["alert"]["type"]); console.log(json_list["bar"]["alert"]["time"]);`

* * *

是时候了解一下:

[![equiman](img/ebe835e1ec966206e00a01bb329a3e86.png)](/equiman) [## 如何在 JSON 中进行 CRUD 操作

### 卡米洛马丁内斯 9 月 23 日 182 分钟阅读

#javascript #json #howto #crud](/equiman/how-to-make-crud-operations-in-json-12he)

* * *

**都是乡亲们！**
**快乐编码** 🖖

[![beer](img/192892baef71a32ea4a5e98a4927b05e.png)T2】](https://github.com/sponsors/deinsoftware)