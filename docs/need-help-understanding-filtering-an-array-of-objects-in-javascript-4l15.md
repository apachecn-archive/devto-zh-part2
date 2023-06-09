# 需要帮助理解:在 Javascript 中过滤对象数组

> 原文：<https://dev.to/leanminmachine/need-help-understanding-filtering-an-array-of-objects-in-javascript-4l15>

我仍然试图用 Javascript 过滤一组对象；花了几个小时才弄明白。

我的后端返回给我这个对象数组。在每个对象内部，都有一个以对象数组作为值的键。在这个数组里，有另一个对象数组，还有另一个数组...是的，变得很混乱。

所以大概是这样的:

```
{
menuEntities: Array(1)
0:
categoryEntities: Array(2)

0:
categoryId: 1
categoryName: "Main"
menuItemEntities: Array(1)
0: {customisableItemEntities: Array(0), description: "Full of delicious beef", enabled: true, foodItemEntities: Array(0), imagePath: "", menuItemName: "Burger"}
length: 1
__proto__: Array(0)
__proto__: Object

1: {categoryId: 2, categoryName: "Drinks", menuItemEntities: Array(1)}
length: 2
__proto__: Array(0)
isSelected: true
menuId: 1
menuName: "Menu 1"
__proto__: Object
length: 1
__proto__: Array(0)
} 
```

Enter fullscreen mode Exit fullscreen mode

我想做的是构建一个过滤函数，如果我的输入文本包含特定的 menuItemName，则返回 true。例如，在我的输入框中输入 burger，所有包含 burger 的餐馆都会出现在我的搜索结果中。

我在[栈溢出](https://stackoverflow.com/questions/45575043/filtering-array-of-objects-by-searching-nested-object-properties)上看到这个帖子，建议使用[一些](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)

经过一点修补，我得到了这个:

```
 this.sortedRestaurants = this.sortedRestaurants.filter(function(
        restaurant
      ) {
        if (_.isEmpty(restaurant.menuEntities) == false) {
          return restaurant.menuEntities[0].categoryEntities.some(category =>
            category.menuItemEntities.some(menuItemEntity =>
              menuItemEntity.menuItemName
                .toLowerCase()
                .includes(val.toLowerCase())
            )
          );
        }
      }); 
```

Enter fullscreen mode Exit fullscreen mode

这适用于当前的用例！

但是我不明白为什么当我最初尝试 forEach 时，这不起作用:

```
this.sortedRestaurants = this.sortedRestaurants.filter(function(
        restaurant
      ) {
        if (_.isEmpty(restaurant.menuEntities) == false) {
          return restaurant.menuEntities[0].categoryEntities.forEach(e => {
            e.menuItemEntities.forEach(menuItemEntity => {
              menuItemEntity.menuItemName
                .toLowerCase()
                .includes(val.toLowerCase());
            });
          });

        }
      }); 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，对于 forEach 函数的情况，includes 不会仍然返回 true 或 false 值..？

你们怎么才能把这个函数写得更好呢？