# 如何获取表格行的内容

> 原文：<https://dev.to/ishwar/how-to-get-the-contents-of-a-table-row--1861>

# 获取 tr 数据值的代码片段

# 获取 td 值的代码片段

```
html +=
 ` <tr data-id=’JavaScript’>
 <td>1</td>
 <td>2</td>
 <td>3</td>
 <td>4</td>
 <td>5</td>
 </tr> `
 document.getElementById(‘tablebody’).innerHTML = html; 
```

#示例)。查找(' tr ')。单击(函数(){

console.log('=========== '，$(this)。attr(" data-id "))；//获取 tr 数据值
console.log('=========== '，$(this)。数据(' id ')；
console.log('========== '，$(此)。find('td:eq(1)')。text()) //获取第一个 td 值

});