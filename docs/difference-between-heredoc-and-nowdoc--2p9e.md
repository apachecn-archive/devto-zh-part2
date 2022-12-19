# Heredoc 和 Nowdoc 的区别🤔

> 原文：<https://dev.to/aaahmedaa/difference-between-heredoc-and-nowdoc--2p9e>

### Heredoc

heredoc 将计算你的变量，并把它们准确地放在你在字符串中放置的位置。
举例:

```
 $name = "Ahmed Khaled";

$here_doc = <<<EOT
My name is "$name" and 
i love programming

EOT;

echo $here_doc; 
```

输出:

```
My name is "Ahmed Khaled" and 
i love programming 
```

### Nowdoc

nowdoc 永远不会计算字符串中的变量

示例:

```
 $name = "Ahmed Khaled";

$here_doc = <<<'EOT'
My name is "$name" and 
i love programming

EOT;

echo $here_doc; 
```

输出:

```
My name is "$name" and 
i love programming 
```