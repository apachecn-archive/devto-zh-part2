# 如何在 JSON 中进行 CRUD 操作

> 原文：<https://dev.to/equiman/how-to-make-crud-operations-in-json-12he>

* * *

在开始 CRUD 操作之前，建议了解:

[![equiman](img/ebe835e1ec966206e00a01bb329a3e86.png)](/equiman) [## 如何在 JSON 中从变量创建属性

### 卡米洛马丁内斯 9 月 21 日 182 分钟阅读

#javascript #json #howto #properties](/equiman/how-to-create-properties-from-variables-in-json-55of)

* * *

CRUD 操作是你作为程序员一生都要做的事情。

可以使用一个巧妙的数据结构来避免 for 循环的强力搜索。使用“字符”名称作为“主键”。

# 权力的游戏举例

## 数据

```
 var got = {
    "Jon Snow": {"name": "Kit Harington", "gender": "Male", "house": "Starks"},
    "Daenerys": {"name": "Emilia Clarke", "gender": "Female", "house": "Targaryens"},
    "Tyrion": {"name": "Peter Dinklage", "gender": "Male", "house": "Lanisters"}
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 添加字符( **C** reate):

`// hidden setup JavaScript code goes in this preamble area var got = { "Jon Snow": {"name": "Kit Harington", "gender": "Male", "house": "Starks"}, "Daenerys": {"name": "Emilia Clarke", "gender": "Female", "house": "Targaryens"}, "Tyrion": {"name": "Peter Dinklage", "gender": "Male", "house": "Lanisters"} }` `// Click [RUN] button to see result var characterObject = { "name": "Bella Ramsey", "gender": "Female", "house": "Mormonts" }; var character = "Lyanna"; got[character] = characterObject; console.log(got)`

## 找人物( **R** ead):

`// hidden setup JavaScript code goes in this preamble area var got = { "Jon Snow": {"name": "Kit Harington", "gender": "Male", "house": "Starks"}, "Daenerys": {"name": "Emilia Clarke", "gender": "Female", "house": "Targaryens"}, "Tyrion": {"name": "Peter Dinklage", "gender": "Male", "house": "Lanisters"} }` `// Click [RUN] button to see result var character = "Tyrion"; // Or whatever var result = got[character]; console.log(result) console.log(result.name) console.log(result.gender) console.log(result.house)`

## 更新字符( **U** pdate):

`// hidden setup JavaScript code goes in this preamble area var got = { "Jon Snow": {"name": "Kit Harington", "gender": "Male", "house": "Starks"}, "Daenerys": {"name": "Emilia Clarke", "gender": "Female", "house": "Targaryens"}, "Tyrion": {"name": "Peter Dinklage", "gender": "Male", "house": "Lanisters"} }` `// Click [RUN] button to see result var character = "Jon Snow"; got[character].house = "Targaryens" var result = got[character]; console.log(result)`

## 删除字符( **D** 删除):

`// hidden setup JavaScript code goes in this preamble area var got = { "Jon Snow": {"name": "Kit Harington", "gender": "Male", "house": "Starks"}, "Daenerys": {"name": "Emilia Clarke", "gender": "Female", "house": "Targaryens"}, "Tyrion": {"name": "Peter Dinklage", "gender": "Male", "house": "Lanisters"} }` `// Click [RUN] button to see result var character = "Tyrion"; delete got[character]; console.log(got)`

# 替代结构

也许这看起来有点好，但是你需要处理 CRUD 操作中的一系列位置。

`// hidden setup JavaScript code goes in this preamble area` `//Data var got = { "index": { "Jon Snow": 0, "Daenerys": 1, "Tyrion": 2 }, "data": [ {"name": "Kit Harington", "gender": "Male", "house": "Starks"}, {"name": "Emilia Clarke", "gender": "Female", "house": "Targaryens"}, {"name": "Peter Dinklage", "gender": "Male", "house": "Lanisters"} ] } var character = "Daenerys"; var index = got.index[character]; var data = got.data[index]; console.log(data);`

* * *

**都是乡亲们！**
**快乐编码** 🖖

[![beer](img/192892baef71a32ea4a5e98a4927b05e.png)T2】](https://github.com/sponsors/deinsoftware)