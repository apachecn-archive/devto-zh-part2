# 找出两个字符串中最长的子序列

> 原文：<https://dev.to/annlin/find-the-longest-subsequence-of-two-strings-709>

### 今天的编码问题是，

求两个字符串中最长的子序列的长度。
给定“abc”和“aec”，最长的子序列字符串是“ac”。

### 快速参考消息，

如果你对子串和子序列感到困惑。

给定两个字符串:" iamnice "，" iam deleted "
，例如最长的子串:" iam"
，例如最长的子串:" iamie "

在这个问题中，我们只关心子序列。

### 如此天真地我可以，

向下列出(这里的空间复杂度是 kaboom)两个字符串的所有可能组合(kaboom 时间复杂度)，然后搜索最长的公共字符串(O(mn)其中 m 和 n 是字符串的长度)。总而言之，极其可怕的时空复杂性。

```
def findCombinationsOfString(string, list, combi):
    if len(string) == 0:
        return

    newCombi = combi + string[0]

    # don't add common strings
    if newCombi not in list:
        list.append(newCombi)

    findCombinationsOfString(string[1:], list, newCombi)

    for i in range(1, len(string)):
        findCombinationsOfString(string[i:], list, combi)

    return list

def nativelyFindLongestSubsequenceOfStrings(str1, str2):
    lengthStr1, lengthStr2 = len(str1), len(str2)

    if lengthStr1 == 0 or lengthStr2 == 0:
        return 0

    if str1 == str2:
        return lengthStr1

    list1 = findCombinationsOfString(str1, [], "")
    # ['a', 'ab', 'abc', 'ac', 'b', 'bc', 'c'] 
    list1 = sorted(list1, key=len, reverse=True)
    # ['abc', 'ab', 'ac', 'bc', 'a', 'b', 'c'] 
    list2 = findCombinationsOfString(str2, [], "")
    # ['a', 'ae', 'aec', 'ac', 'e', 'ec', 'c'] 
    list2 = sorted(list2, key=len, reverse=True)
    # ['aec', 'ae', 'ac', 'ec', 'a', 'e', 'c'] 
    for i in range(len(list1)):
        for j in range(len(list2)):
            if list1[i] == list2[j]:
                return len(list1[i])

    return 0

print nativelyFindLongestSubsequenceOfStrings("", "") 
```

### 更好的办法是，

将两个字符串放入 2D 数组。(我试图得出我的解释，参见下一节的叙述性解释)

[![my amazing drawing](../Images/8da6d3c29d185c347f7555d77b9f76a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sMhbdr3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bg61wk8ttilymctync6d.jpg)

### 多以文字解释。

我们将两个字符串放入 2D 数组。遍历 2D 数组，找到所有常见的字符。如果两个字符串中的字符相同，则递增 1(表示子序列中有 1 个字符)。

为了得到最长的子序列，我们需要考虑之前出现的所有常见字符的可能组合。所以我们增加 1 +得到之前的任何(可能的组合)值。

很快我们意识到可能的组合值有 3 种可能的情况；它可以来自左上框、顶框和左框。因为我们正在寻找最长的子序列，所以我们得到了 3 个可能值中的最大值。

如果当前索引显示两个字符串中的两个字符是相同的，我们增加 1 + max(左上值，上值，左值)。

直到到达右下角的最后一个索引。它应该包含两个字符串中最长的子序列。

Tada.

### 代码、

当然是用 Python。
时间复杂度:O(mg)，开个玩笑。是 O(mn)，其中 m 和 n 是字符串的长度
空间复杂度:O(mn)，2D 数组占用空间。

```
def findLongestSubsequenceString(str1, str2):
    lengthStr1, lengthStr2 = len(str1), len(str2)

    # if one of the strings is empty, return length of non empty string
    # we can easily use a variable for magic number 0\. e.g. EMPTY_STRING = 0
    if lengthStr1 == 0 or lengthStr2 == 0:
        return 0

    # do you think it's a good idea to catch specific case like this?
    # if the possibility of both strings being same is high, this is a legit case I think.
    if str1 == str2:
        return lengthStr1

    # create 2D array with length of str1 and str2
    # I think this syntax is cool AF.
    memo = [[0 for i in range(lengthStr2)] for i in range(lengthStr1)]

    for i in range(lengthStr1):
        for j in range(lengthStr2):

            topLeft = top = left = 0
            # need to check if the indices are within range of 2D array,
            # else the code will throw you into the sea (I meant throw out of range error)
            if i > 0 and j > 0:
                topLeft = memo[i - 1][j - 1]

            if i > 0:
                top = memo[i - 1][j]

            if j > 0:
                left = memo[i][j - 1]

            # get the maximum value of possible subsequence combinations
            memo[i][j] = max(topLeft, top, left)

            # increment 1 if current characters are the same
            # we can do it up there but I think it looks clearer if we separate out this logic
            if str1[i] == str2[j]:
                memo[i][j] = memo[i][j] + 1

    return memo[lengthStr1 - 1][lengthStr2 - 1]

print findLongestSubsequenceString('abc', 'bca')
# 2 
# 2D Array looks likes this:
# [1, 0, 0]
# [0, 1, 1]
# [0, 1, 2] 
```

### 我们错过了什么，

这段(狗屎)代码只适合独特的字符串。如果有重复字符(opp)怎么办。我刚开始玩动态编程，所以我需要更多地学习它。到目前为止，这是我认为最好的解决方案。我们也可以递归地做。我还没有想到那种方法。(下一部分？)

如果我遗漏了任何边缘案例，请发表评论。:<

### 我快速瞥了一眼(谷歌搜索解决方案:>)，

使用`suffix` thingy 有更好的解决方案，但我相信我的大脑已经受够了这个问题。我们很快就会回到这个`suffix`的事情上来。

### 好了，

再见。

我们能对代码进行颜色编码吗？