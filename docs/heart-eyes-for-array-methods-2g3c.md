# 阵列方法的心眼

> 原文：<https://dev.to/veronicorn/heart-eyes-for-array-methods-2g3c>

已经快三个月了(又名半路！)进入了我在编码训练营的第一次强化体验，很明显，对象和数组对于 JavaScript 新手来说也非常重要。对象是键值对(*就像一个单词及其在字典*中的相应定义)，而数组是一种特殊类型的对象，是一个有序列表，这在处理大量相同类型的数据时非常有用，比如名称集合。

例如，随机颜色的数组被标注为:

```
let colors = ['yellow', 'black', ‘blue’, ‘green’] 
```

Enter fullscreen mode Exit fullscreen mode

使用数组，JavaScript 使您能够进行基本的编辑，如添加、删除和更新数据，并帮助您用有限的代码对信息进行排序。这些被称为*数组方法*。这些很酷的小机制太多了，我自己还没有尝试和测试过其中的一小部分，但是这里有一些我最喜欢的。

**推送**
向数组末尾添加新元素

```
let colors = ['yellow', 'black', ‘blue’, ‘green’]

colors.push('white');

alert(colors) 
// Output will be yellow, black, blue, green, white 
```

Enter fullscreen mode Exit fullscreen mode

**拼接**
从数组中的任意位置删除项目。你传递的参数指示*你在数组中的什么地方开始*和*要移除多少项*。记住索引规则:数组中的第一项被定义为 0，第二项为 1，第三项为 2，依此类推。

```
let colors = ['yellow', 'black', ‘blue’, ‘green’, 'white'] 
// let’s remove black and blue out of there!

colors.splice(1, 2) 
// so starting at index 1 (black), remove 2 items (black, blue)

alert(colors) 
// Output will now be yellow, green, white 
```

Enter fullscreen mode Exit fullscreen mode

拼接是双倍酷的，因为你不仅可以从数组的任何地方删除项目，还可以同时插入新的项目。

```
let colors = ['yellow', 'black', 'blue', 'green', 'white'] 
// let’s remove blue and green and add a bit more flair

colors.splice(2, 2, 'rose gold')

alert(colors) 
// Output will be yellow, black, rose gold, white 
```

Enter fullscreen mode Exit fullscreen mode

我希望在我的新兵训练营之旅以及以后的时间里，能够使用并了解更多这些小英雄(其中许多你可以在这里找到)。数组万岁！