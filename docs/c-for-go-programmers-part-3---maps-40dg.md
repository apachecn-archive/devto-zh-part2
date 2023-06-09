# 面向 Go 程序员的 C++:第 3 部分-映射

> 原文：<https://dev.to/dayvonjersen/c-for-go-programmers-part-3---maps-40dg>

> **免责声明** : *本系列标题的灵感来自 Go Wiki* **上的一篇精彩文章[，但是，与那篇文章](https://github.com/golang/go/wiki/GoForCPPProgrammers)** *不同，本系列中的任何信息都不应被视为合理的建议。作者假定读者在现代 C++和 Go 方面的能力远远超过作者自己。这实际上是一个几乎不加掩饰的游戏 devlog*

`map`是围棋中*轻松*最差**的**内置数据类型。我有一个原因的大纲，但我们在这里不是关于消极。另外，我很确定那篇文章已经有人写过了。

不，我们不在乎那种生活。我们正在探索 C++中美妙而激动人心的新可能性！！！...通过重新发明轮子、鸭宝宝综合症和避开明确定义的最佳实践的组合。

## 如果可以{

在 Go 中，您可以一步访问一个地图值并检查它是否存在:

```
m := map[string]int{
    "one": 1,
    "two": 2,
}

one, ok := m["one"]     // 1, true
two, ok := m["two"]     // 2, true
three, ok := m["three"] // 0, false 
```

Enter fullscreen mode Exit fullscreen mode

`std::map`在 C++中确实有`operator[]`但是它的行为不同:

> 返回一个值的引用，该值映射到一个与 key 等效的键，如果这样的键不存在，则执行插入。

这不是我们想要的。

`std::map::find`然而，将允许我们先确定一个值是否存在。

在 C++中我们没有多个返回值，所以我们必须通过引用传递一个变量来获取值。

这里什么都没有:

```
#include <map>

template<typename K, typename V>
bool mapValue(std::map<K,V> m, K key, V& value) {
    auto it = m.find(key);
    if(it != m.end()) {
        value = it->second;
        return true;
    }
    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

稍微修改一下上面的 Go 示例，像这样使用它:

```
std::map<std::string,int> m;
m["one"] = 1;
m["two"] = 2;

int one;
bool success = mapValue(m, std::string("one"), one);
if(success) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

w00t

* * *

这实际上是我第一次做一个模板化的函数，实际上还挺有趣的，尽管我知道我正在走向毁灭(首先你开始不时地在你的代码中散布一些漂亮的小泛型，然后你开始做元编程，接下来你知道你正在编写编译时的俄罗斯方块并为 Boost 做贡献)

应该注意的是，这里的实现并不适用于所有的*类型，尤其是 C 风格的`char*`字符串(因此我在示例用法中使用了`std::string`)。*

 *此外，我可能根本不应该使用`std::map`在我的磁贴地图中存储特殊磁贴的信息，但我并不担心它，它很好，不要担心它*