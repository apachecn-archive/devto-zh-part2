# 排序方法

> 原文：<https://dev.to/alexbryant23/sort-method-3631>

```
There are a lot of things that can be done with arrays. Being one of the most basic ways forms of data it is nice to know what all you can do to manipulate them. Today we will be discussing .sort. 

.sort takes in an array, and puts them in a predetermined order. This order is typically either numerical of alphabetical. There are some odd rules that come with .sort like if a capital letter is thrown into an array then the array will be sorted by capital letters first. And if the array contains numbers and letters then  it will sort numbers first. 

Lets take a look at what this looks like in practice. 
```

var months = ['三月'，'一月'，'二月'，'十二月']；
months . sort()；
console.log(月)；
//预期输出:数组["Dec "，" Feb "，" Jan "，" March"]

var array1 = [1，30，4，21]；
array 1 . sort()；
console . log(array 1)；
//预期输出:数组[1，4，21，30]

```
Here we can see that the array was sorted alphabetically as expected, and numerically as expected. Now if we change things up a little it could look like this. 
```

var months = ['三月'，' 23 '，'二月'，'十二月']；
months . sort()；
console.log(月)；
//预期输出:Array [23，" Dec "，" feb "，" march"]

```
Now that we added numbers and changed some capitalization this array has been sorted by numbers first, then capital letters, and finally in alphabetically. Data is a great thing but if you don't know how to manipulate it then you are not going to be able to use it! 
```