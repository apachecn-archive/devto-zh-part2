# 学习编程语言的细节？

> 原文：<https://dev.to/robertcoopercode/learning-the-granular-details-of-a-programming-language-pln>

我正在读《JavaScript:权威指南》,这是一本很长的书，因为它基本上涵盖了与 JavaScript 相关的所有内容。这让我想知道:我学习 JavaScript 语言的粒度部分是在浪费时间吗？

例如，JavaScript 有一个正零(`0`)和一个负零(`-0`)的概念。在 JavaScript 中，这两个值是相等的，除非使用除数:

```
var positiveZero = 0;
var negativeZero = -0;
positiveZero === negativeZero; // => true
1/postiveZero === 1/negativeZero; // => false 
```

让我们现实一点，我记得这种奇怪行为的几率有多大，在使用 JavaScript 时有用的几率有多大？

大家是怎么想的？花时间学习这种语言的晦涩部分是值得的。如果不是，那么深入一门语言真正深层部分的书值得读吗？