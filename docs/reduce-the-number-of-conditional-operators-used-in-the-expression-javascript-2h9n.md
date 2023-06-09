# JavaScript:减少表达式中使用的条件运算符的数量

> 原文：<https://dev.to/kvharish/reduce-the-number-of-conditional-operators-used-in-the-expression-javascript-2h9n>

我们都会遇到这样的情况，表达式中使用的条件运算符的数量更像下面的例子。

```
const firstCondition = true,
      secondCondition = true,
      thirdCondition = true,
      fourthCondition = false,
      fifthCondition = true;

if(firstCondition && secondCondition && thirdCondition && fourthCondition && fifthCondition) {
    console.log("All the conditions met expectation");  
}
else {
    console.log("All the conditions did not meet expectation");
}

if(firstCondition || secondCondition || thirdCondition || fourthCondition || fifthCondition) {
    console.log("Atleast one of the conditions met expectation");  
}
else {
    console.log("None of the conditions met the expectation");
}

//Output:
//All the conditions did not meet the expectation
//At least one of the conditions met expectation 
```

Enter fullscreen mode Exit fullscreen mode

ESLint、SonarQube 等代码质量工具实际上会建议我们优化代码，减少条件操作符。那么我们该怎么做呢？

有很多方法可以解决这个问题。我准备给出一个更简单的方法(更新)。如果你有更简单或更好的解决方案，请在评论区留下。

```
const firstCondition = true,
      secondCondition = true,
      thirdCondition = true,
      fourthCondition = false,
      fifthCondition = true;

const conditionsToCheck = [firstCondition, secondCondition, thirdCondition, fourthCondition, fifthCondition]

if(conditionsToCheck.every(condition => condition)) {
    console.log("All the conditions met expectation");
}
else {
    console.log("All the conditions did not meet expectation");
}

if(conditionsToCheck.some(condition => condition)) {
    console.log("Atleast one of the conditions met expectation");
}
else {
    console.log("None of the conditions met the expectation");
}

//Output:
//All the conditions did not meet the expectation
//At least one of the conditions met expectation 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以在如下条件下混合，它将没有任何问题。

```
const conditionsToCheck = [firstCondition, secondCondition || sixthCondition, thirdCondition, fourthCondition, fifthCondition && seventhCondition]; 
```

Enter fullscreen mode Exit fullscreen mode

除非你支持 IE8 或更低版本，否则你可以使用数组的一些和所有方法。

希望这篇文章有帮助。