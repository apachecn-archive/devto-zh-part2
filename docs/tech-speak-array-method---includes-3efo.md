# 技术专家:数组方法。包含

> 原文：<https://dev.to/reduncan/tech-speak-array-method---includes-3efo>

当开始学习 JavaScript 时，我们知道从数组中获取信息是通过使用 for 循环来完成的。我们还了解到，编写 for 循环涉及大量代码。因为 for 循环中涉及大量代码，所以创建了数组方法。

数组方法不仅减少了必须编写的代码量，还提高了代码的可读性和整洁性。有一个数组方法的完整列表，*。concat，。过滤器，。查找，。的索引。地图，还有很多*。你可以通过[点击这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#)找到内置方法的完整列表。今天我们来看看*的内部运作。包括*。

我们家里有一系列的植物...

```
const plants = [
        "cactus", 
        "fern",
        "bonsai",
        "aloe",
        "lily",
        "orchid",
        "fiddle-leaf"
] 
```

我们创建了一个应用程序，我们的朋友可以用它来查看我们家里目前有哪些植物。为此，我们有下面的代码...

```
const inHouse = function() {
        htmlStr = '';
        userInput = $(`#input`).val().tolowercase();
        weHave = plants.includes(userInput);
        if (weHave === true) {
            htmlStr += `We have a ${userInput} in our house.`;
        } else {
            htmlStr += `We do not have a ${userInput} in our house.`;
            }
        }; 
```

当朋友想知道我们是否有某种植物时，他们会进入我们的应用程序，输入植物。然后它会告诉他们我们家现在是否有这种植物。

现在让我们看看如果不使用*代码会是什么样子。包括*，但是使用了一个*用于循环*...

```
const inHouse = function() {
        htmlStr = '';
        weHave = false
        userInput = $(`#input`).val().tolowercase();
        for (let i = 0; i < plants.length; i++) {
            if (plants[i] === userInput) {
                weHave = true
            };
        if (weHave === true) {
            htmlStr += `We have a ${userInput} in our house.`;
        } else {
            htmlStr += `We do not have a ${userInput} in our house.`;
            }
        }; 
```

通过比较这两个代码块，您可以看到，我们必须添加 4 行代码来判断用户的输入是否包含在我们的数组中。我们添加了一个 for 循环，在 for 循环内部，我们还添加了一个 if 语句。虽然这段代码阅读起来并不困难，但看起来确实有些混乱，而且一开始很难理解。但是这里的重点是*内部发生了什么。包括*。

达到*。包括*:

*   我们首先要补充的是，weHave 是假的
*   使用 for 循环
*   for 循环设置为从 0(数组中第一个项目的索引)开始，迭代数组的次数与数组的长度(arr.length)一样多，最后递增 I，这样我们就可以从数组中取出该索引的项目
*   当我们遍历数组时，我们将 userInput 与数组中的每一项进行比较
*   如果第一个索引匹配 userInput，它将在变量 weHave 中存储 true，如果第一个索引不匹配 userInput，它将移到下一个索引并进行比较。它将遍历数组，直到找到匹配项或 for 循环终止。如果它没有找到匹配，并且 for 循环终止，我们将保持存储为 false。

这就是为什么。包含作品！

直到下次:)