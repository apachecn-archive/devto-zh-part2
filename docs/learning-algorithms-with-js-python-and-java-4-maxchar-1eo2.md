# 用 JS、Python 和 Java 4 学习算法:MaxChar

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-4-maxchar-1eo2>

这是我试图用三种不同的语言学习斯蒂芬·格里德的 Udemy 课程的第四篇文章。

首先我引用今天的问题:

> - Directions
> 给定一个字符串，返回该字符串中最常用的字符
> 。
> -示例
> maxChar(" abccccccd ")= = = " c "
> maxChar("苹果 1231111 ")= = " 1 "

JavaScript:

```
function maxChar(str) {
    const charMap = {};
    let max = 0;
    let maxChar = '';

    for (let char of str) {
        if (charMap[char]) {
            charMap[char]++
        } else {
            charMap[char] = 1;
        }
    }

    for (let char in charMap) {
        if (charMap[char] > max) {
            max = charMap[char];
            maxChar = char;
        }
    }

    return maxChar;
} 
```

Enter fullscreen mode Exit fullscreen mode

Stephen 将第一个 for 循环重写为:

```
 for (let char of str) {
        charMap[char] = charMap[char] + 1 || 1;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是可能的，因为`anObject['nonexistentKey']`返回`undefined`，`undefined + 1`返回`NaN`，这是假的。

Python:

```
def max_char(str):
    char_map = {}
    max = 0
    max_char = ''

    for char in str:
        char_map.setdefault(char, 0)
        char_map[char] += 1

    for char in char_map:
        if char_map[char] > max:
            max = char_map[char]
            max_char = char

    return max_char 
```

Enter fullscreen mode Exit fullscreen mode

在 Python 中，你不能像在 JS 中那样尝试`char_map[char] += 1`，因为如果键还不存在，它会抛出一个 KeyError。

更简洁地:

```
def max_char(str):
    char_map = {}

    for char in str:
        char_map.setdefault(char, 0)
        char_map[char] += 1

    return max(char_map, key=char_map.get) 
```

Enter fullscreen mode Exit fullscreen mode

来源:[https://stack overflow . com/questions/268272/getting-key-with-maximum-value-in-dictionary](https://stackoverflow.com/questions/268272/getting-key-with-maximum-value-in-dictionary)
`max(char_map, key=char_map.get)`与`max(char_map.keys(), key=char_map.get)`相同。需要
`key=char_map.get`来查找字典中*值*最大的(键，值)元组对。`max({'a': 2, 'b': 1})`返回‘b’。

Java:

```
import java.util.HashMap;
import java.util.Map;

public static char maxChar2(String str) {
    Map<Character, Integer> charMap = new HashMap<>();

    for (char chr : str.toCharArray()) {
        Integer chrCount = charMap.get(chr);
        if (chrCount == null) {
            chrCount = 0;
        }
        charMap.put(chr, chrCount + 1);
    }

    int max = 0;
    char maxChar = '\0';

    for (Map.Entry<Character, Integer> entry : charMap.entrySet()) {
        int value = entry.getValue();
        if (value > max) {
            max = value;
            maxChar = entry.getKey();
        }
    }

    return maxChar;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用流:

```
public static char maxChar(String str) {
    Map<Character, Long> charMap = str.chars()
            .mapToObj(i -> (char) i)
            .collect(Collectors.groupingBy(
                    c -> c, Collectors.counting()));

    return charMap.entrySet().stream()
            .collect(Collectors.groupingBy(
                    Map.Entry::getValue,
                    TreeMap::new,
                    Collectors.mapping(
                            Map.Entry::getKey, Collectors.toList())))
            .lastEntry()
            .getValue()
            .get(0);
} 
```

Enter fullscreen mode Exit fullscreen mode

return 语句可以更简单:

```
 return charMap.entrySet().stream()
            .max(Map.Entry.comparingByValue())
            .get()
            .getKey(); 
```

Enter fullscreen mode Exit fullscreen mode

参考资料:
[https://code review . stack exchange . com/questions/133058/print-max-occuring-character-in-a-string](https://codereview.stackexchange.com/questions/133058/print-max-occurring-character-in-a-string)
[https://stack overflow . com/questions/48280361/how-to-count-occurrences-for-each-value-in-multimap-Java](https://stackoverflow.com/questions/48280361/how-to-count-occurrences-for-each-value-in-multimap-java)
[https://stack overflow . com/questions/42060294/using-Java 8](https://stackoverflow.com/questions/42060294/using-java8-stream-to-find-the-highest-values-from-map)