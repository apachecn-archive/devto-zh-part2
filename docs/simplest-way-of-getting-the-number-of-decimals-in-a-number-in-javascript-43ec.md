# JavaScript 中获取一个数的小数位数的最简单方法

> 原文：<https://dev.to/ishwar/simplest-way-of-getting-the-number-of-decimals-in-a-number-in-javascript-43ec>

你可以得到任意数字的小数位数

var count decimals = function(value){
if(math . floor(value)= = = value)返回 0；
返回值. toString()。拆分(“.”)[1].长度| | 0；
}

console . log(count decimals(9.11))//2
console . log(count decimals(9))//0