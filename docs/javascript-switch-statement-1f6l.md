# JavaScript Switch 语句

> 原文：<https://dev.to/christiangraves/javascript-switch-statement-1f6l>

本周我将继续我的条件语句主题，并介绍 ***转换*** 语句。switch 语句基本上是 [if-else if 语句](https://dev.to/christiangraves/conditional-statements-in-javascript-56pk)的表亲。它提供了与 else if 语句相同的功能，但是它没有对初始 if 和后续 else if 使用计算条件，而是使用一个计算表达式来代替 cases。我在下面提供了 switch 语句的语法。

```
 switch(expression){

    case 0:
        //some executed code
        break;

    case 1:
        //some executed code
        break;

    case 2: 
        //some executed code
        break;

    default:
        //some executed code
} 
```

从上面的语法可以看出，switch 语句接受一个表达式，而不是一个条件。您还可以看到“cases”替换了条件语句中的 else if 语句。对接受的表达式进行计算，生成的值应该与其中一种情况相匹配，以执行该情况下的代码。我说值“应该”匹配其中一种情况，但如果不匹配，这就是为什么默认情况在那里。如果没有匹配的事例，它将充当 else 语句。与条件语句中的 else 一样，默认情况是可选的，也不是必需的。请注意，您需要在每个 case 的末尾使用 break 语句，这样程序就知道要从 switch 语句中移出。你可以在下面看到一个简单的例子，我已经包含了一个[交互式 jsbin 和](https://jsbin.com/jevabeq/edit?js,console)。

```
 const x = 3;
let month = "";

switch(x){
  case 1:
    month = "January";
    break;

  case 2:
    month = "February";
    break;

  case 3:
    month = "March";
    break;

  case 4:
    month = "April";
    break;

  case 5:
    month = "May";
    break;

  case 6:
    month = "June";
    break;

  default:
    month = "Not one of the first 6 months";

} 
```

在示例中，您可以看到 x 的值被计算为 3，因为这是变量的设置值。switch 语句将使用严格比较(===)将值 3 与每个案例号进行比较。由于 x 匹配案例 3，变量 month 将被设置为“三月”。

关于 switch 语句中的 cases，需要注意的另一点是，它们不一定是数字，而是任何值。我们可以将变量 x 设置为月份名称字符串，并将事例值更改为一年的前六个月。只要事例的值也是字符串，switch 语句就仍然有效。关于开关，我想说的最后一点是，可以使用多种情况来执行相同的代码。这可以帮助您节省编写代码的时间，并提高代码的可读性。下面我有一个小例子。

```
const x = "April";
let quarter = "";

switch(x){
  case "January":
  case "February":
  case "March":
    quarter = "Month is in Q1";
    break;

  case "April":
  case "May":
  case "June":
    quarter = "Month is in Q2";
    break;

  default:
    quarter = "Month is not in Q1 or Q2";

} 
```