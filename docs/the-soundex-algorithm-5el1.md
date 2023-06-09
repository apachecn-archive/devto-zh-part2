# SoundEx 算法

> 原文：<https://dev.to/lefebvre/the-soundex-algorithm-5el1>

Soundex 是一种语音算法，用于按声音索引名称，如英语中的发音。它通常与数据库一起使用，以帮助搜索，并内置于许多数据库引擎中，如 PostgreSQL 和 MySQL。默认情况下，SQLite 中不包含 SoundEx，在某些情况下，您可能希望在搜索时使用它。

幸运的是，算法并不那么难。你可以在维基百科上阅读更多关于 [SoundEx 的内容，但以下是一般步骤:](https://en.wikipedia.org/wiki/Soundex)

1.  保留名字的第一个字母，去掉所有其他出现的 a，e，I，o，u，y，h，w。
2.  用数字替换辅音，如下所示(第一个字母后):
    *   b，f，p，v → 1
    *   c，g，j，k，q，s，x，z → 2
    *   d，t → 3
    *   l → 4
    *   m，n → 5
    *   r → 6
3.  如果原姓名中有两个或两个以上同号字母相邻(步骤 1 之前)，只保留首字母；同样，由“h”或“w”分隔的具有相同数字的两个字母被编码为单个数字，而由元音分隔的这种字母被编码两次。这条规则也适用于第一个字母。
4.  如果单词中的字母太少，无法分配三个数字，则添加零，直到有三个数字。如果超过 3 个字母，只保留前 3 个数字。

一般来说，实现这个算法非常简单。这是一个使用 [Xojo](http://www.xojo.com) 的 SoundEx 算法的示例函数，根据需要，它应该很容易翻译成其他语言:

```
Private Function SoundEx(word As Text) As Text
  Const kLength As Integer = 4

  Dim value As Text

  Dim size As Integer = word.Length

  ' Make sure the word is at least two characters in length
  If (size > 1) Then
    word = word.Uppercase

    ' Convert the word to a character array for faster processing
    Dim chars() As Text = word.Split

    ' For storing the SoundEx character codes
    Dim code() As Text

    ' The current and previous character codes
    Dim prevCode As Integer = 0
    Dim currCode As Integer = 0

    ' Add the first character
    code.Append(chars(0))

    Dim loopLimit As Integer = size - 1
    ' Loop through all the characters and convert them to the proper character code
    For i As Integer = 0 To loopLimit
      Select Case chars(i)
      Case "H", "W"
        currCode = -1
      Case "A", "E", "I", "O", "U", "Y"
        currCode = 0
      Case "B", "F", "P", "V"
        currCode = 1
      Case "C", "G", "J", "K", "Q", "S", "X", "Z"
        currCode = 2
      Case "D", "T"
        currCode = 3
      Case "L"
        currCode = 4
      Case "M", "N"
        currCode = 5
      Case "R"
        currCode = 6
      End Select

      If i > 0 Then
        ' two letters With the same number separated by 'h' or 'w' are coded as a single number
        If currCode = -1 Then currCode = prevCode

        ' Check to see if the current code is the same as the last one
        If currCode <> prevCode Then
          ' Check to see if the current code is 0 (a vowel); do not proceed
          If currCode <> 0 Then
            code.Append(currCode.ToText)
          End If
        End If
      End If

      prevCode = currCode

      ' If the buffer size meets the length limit, then exit the loop
      If (code.Ubound = kLength - 1) Then
        Exit For
      End If
    Next

    ' Pad the code if required
    size = code.Ubound + 1
    For j As Integer = size To kLength - 1
      code.Append("0")
    Next

    ' Set the return value
    value = Text.Join(code, "")
  End If

  ' Return the computed soundex
  Return value
End Function 
```

Enter fullscreen mode Exit fullscreen mode

您可以像这样调用 SoundEx 函数:

```
Dim result As Text
result = SoundEx("Robert")   ' R163
result = SoundEx("Rupert")   ' R163 
result = SoundEx("Rubin")    ' R150 
result = SoundEx("Ashcraft") ' A261
result = SoundEx("Ashcroft") ' A261
result = SoundEx("Tymczak")  ' T522
result = SoundEx("Pfister")  ' P236 
```

Enter fullscreen mode Exit fullscreen mode

通过保存 SoundEx 结果(在 SQLite、JSON 或任何地方),您可以再次使用它们来与其他值的 SoundEx 结果进行比较，以便更好地搜索。