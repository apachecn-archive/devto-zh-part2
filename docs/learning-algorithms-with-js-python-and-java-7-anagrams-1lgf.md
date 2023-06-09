# 用 JS、Python 和 Java 7 学习算法:字谜

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-7-anagrams-1lgf>

这是我试图用三种不同的语言学习斯蒂芬·格里德的 Udemy 课程的第七篇文章。JavaScript 解决方案是由斯蒂芬的[。我试着把它“翻译”成 Python 和 Java。](https://github.com/StephenGrider/AlgoCasts/blob/master/completed_exercises/chunk/index.js)

今天的问题是:

> 检查提供的两个字符串是否互为变位词。如果一个字符串使用相同数量的相同字符
> ，那么它就是另一个字符串的变位词。只考虑字符，不考虑空格
> 或标点符号。考虑大写字母与小写字母相同
> -示例
> 变位词(‘铁路安全’，‘童话’)->True
> 变位词(‘铁路！安全！”，《童话故事》)- >真
> 字谜(‘嗨那儿’，‘再见那儿’)->假

# 1:统计每个字母

JavaScript:

```
function anagrams(stringA, stringB) {
  const charMapA = buildCharMap(stringA);
  const charMapB = buildCharMap(stringB);

  if (Object.keys(charMapA).length !== Object.keys(charMapB).length) {
    return false;
  }

  for (let char in charMapA) {
    if (charMapA[char] !== charMapB[char]) {
      return false;
    }
  }

  return true;
}

function buildCharMap(str) {
  const charMap = {};

  for (let char of str.replace(/[^\w]/g, '').toLowerCase()) {
    charMap[char] = charMap[char] + 1 || 1;
  }

  return charMap;
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
import re
from collections import Counter

def anagrams(string_a: str, string_b: str) -> bool:
    char_map_a = build_counter(string_a)
    char_map_b = build_counter(string_b)

    if len(char_map_a.keys()) != len(char_map_b.keys()):
        return False

    for char in char_map_a.keys():
        if char not in char_map_b or char_map_a[char] != char_map_b[char]:
            return False

    return True

def build_counter(string: str) -> Counter:
    return Counter(re.sub(r'[^\w]', '', string, flags=re.UNICODE).lower()) 
```

Enter fullscreen mode Exit fullscreen mode

其实这个也管用:

```
import re
from collections import Counter

def anagrams(string_a: str, string_b: str) -> bool:
    return build_counter(string_a) == build_counter(string_b)

def build_counter(string: str) -> Counter:
    return Counter(re.sub(r'[^\w]', '', string, flags=re.UNICODE).lower()) 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
import java.util.Map;
import java.util.stream.Collectors;

public static boolean anagrams(String stringA, String stringB) {
    Map<Character, Long> charMapA = buildCharMap(stringA);
    Map<Character, Long> charMapB = buildCharMap(stringB);

    if (charMapA.keySet().size() != charMapB.keySet().size()) {
        return false;
    }

    for (char chr : charMapA.keySet()) {
        if (!charMapB.containsKey(chr) || !charMapA.get(chr).equals(charMapB.get(chr))) {
            return false;
        }
    }
    return true;
}

private static Map<Character, Long> buildCharMap(String str) {
    return str.replaceAll("[^\\w]", "")
            .toLowerCase()
            .chars()
            .mapToObj(i -> (char) i)
            .collect(Collectors.groupingBy(c -> c, Collectors.counting()));
} 
```

Enter fullscreen mode Exit fullscreen mode

# 2:排序比较

JavaScript:

```
function anagrams(stringA, stringB) {
  return cleanString(stringA) === cleanString(stringB);
}

function cleanString(str) {
  return str
    .replace(/[^\w]/g, '')
    .toLowerCase()
    .split('')
    .sort()
    .join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

Python:

```
import re

def anagrams(string_a: str, string_b: str) -> bool:
    return clean_string(string_a) == clean_string(string_b)

def clean_string(string: str) -> str:
    lower = re.sub(r'[^\w]', '', string, flags=re.UNICODE).lower()
    return ''.join(sorted(lower)) 
```

Enter fullscreen mode Exit fullscreen mode

Java:

```
import java.util.stream.Collectors;

public static boolean anagrams(String stringA, String stringB) {
    return cleanString(stringA).equals(cleanString(stringB));
}

private static String cleanString(String str) {
    return str.replaceAll("[^\\w]", "")
            .toLowerCase()
            .chars()
            .mapToObj(i -> (char) i)
            .sorted()
            .map(String::valueOf)
            .collect(Collectors.joining());
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望你喜欢这个。我将感谢任何意见和反馈。