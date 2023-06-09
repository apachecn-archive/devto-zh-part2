# 评论我的 JS 解决方案

> 原文：<https://dev.to/ztickm/critique-my-js-solution-to-this-codewars-kata--3icl>

## 武士形

[练习](https://www.codewars.com/kata/554e4a2f232cdd87d9000038/train/javascript)基本上要求将任何‘T’转换为‘A ’,将任何‘C’转换为‘G ’,反之亦然。

## 我的解决方案

我知道这可能不是典型的解决方案，但这是我脑海中闪现的第一件事。
我先这样写的:

```
function DNAStrand(dna){
let map = Array.prototype.map;
let reversedDna = map.call(dna, function(x) { 
  let y;
  switch(x){
    case 'A': y = 'T'; break;
    case 'T': y = 'A'; break;
    case 'G': y = 'C'; break;
    case 'C': y = 'G'; break;
  }
  return y; 
});
return reversedDna.join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我心想:“我可以去掉那个 y 变量。”于是我照做了:

```
function DNAStrand(dna){
let map = Array.prototype.map;
let reversedDna = map.call(dna, function(x) { 
  let y;
  switch(x){
    case 'A': return 'T'; break;
    case 'T': return 'A'; break;
    case 'G': return 'C'; break;
    case 'C': return 'G'; break;
  }
});
return reversedDna.join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我想:“我可以去掉换行，因为 return 语句已经开始换行了，不是吗？”

```
function DNAStrand(dna){
let map = Array.prototype.map;
let reversedDna = map.call(dna, function(x) { 
  let y;
  switch(x){
    case 'A': return 'T'; 
    case 'T': return 'A'; 
    case 'G': return 'C'; 
    case 'C': return 'G'; 
  }

});
return reversedDna.join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

有什么方法可以改进我的解决方案吗？你有别的方法做那件事吗？请在评论中留下你的评论