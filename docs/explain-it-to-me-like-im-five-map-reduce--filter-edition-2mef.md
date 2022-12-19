# 把它解释给我听，就像我五岁一样:。地图，。减少，&。过滤器版本

> 原文：<https://dev.to/rcmaples/explain-it-to-me-like-im-five-map-reduce--filter-edition-2mef>

我很难理解如何使用 map、reduce 和 filter 来迭代数组(或者对象数组)🙄).

我通常使用 for 循环(如果需要的话，还可以嵌套 for 循环)，但是我真的想切换到 map、reduce 和 filter 来处理各种事情。我只是无法理解它是如何工作的，它在做什么。

这里有一个示例代码，我想我可以使用 map/reduce/filter 来获得更清晰的结果。

```
const jsIngredients = [
    {"ingredient-1":"chicken"},
    {"ingredient-2":"brocolli"},
    {"ingredient-3":"cheese"}
];

let ingredientString = "";

for (let k = 0; k<jsIngredients.length; k++) { 
    if (jsIngredients[k].value) { // if non-empty
        ingredientString +=  `${jsIngredients[k].value},`;
        // ingredientString = "chicken,brocolli,cheese," 
        }
    }
ingredientString = ingredientString.slice(0,ingredientString.length-1);
// ingredientString = "chicken,brocolli,cheese" 
```

Enter fullscreen mode Exit fullscreen mode

有帮助吗？
🍻