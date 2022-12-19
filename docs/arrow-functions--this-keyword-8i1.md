# 箭头功能+此关键字

> 原文：<https://dev.to/veronicorn/arrow-functions--this-keyword-8i1>

技术博客文章编号 trois，向你走来！

在我上一篇关于箭头函数的帖子的最后，我简单地调侃了一下箭头函数与 JavaScript 关键字 *this* 的特殊关系。关于这个*有太多东西要学了(谷歌在这个话题上给出了超过 309，000 个点击量！)，但这是我目前所了解到的关于可爱的*胖箭*的情况。*

(旁注:正如我在上一篇文章的讨论部分中的一些温和的辩论所证明的，箭头函数有许多名称！我甚至刚刚看到它被称为*哈希火箭*，这是一个被 Ruby 爱好者戏称的术语。我不得不说，这可能是我最喜欢的由任何人给任何事物起的名字。)

据我所知，*这个*有着和超级粉丝一样多的憎恨者。在所有经典函数表达式中使用它可能会令人困惑，而且看起来不可靠，因为 *this* 依赖于它被调用的环境的**上下文**，例如，如果 *this* 位于方法中的函数内， *this* 将引用全局范围而不是方法。

进入...箭头功能！

正如你在我下面的示例代码中看到的，*这个*不幸地没有按照我想要的方式工作。弹出的警告显示“爱咬人”、“爱抓人”和“爱破坏”，但不包括猫的可怕名字，龙。*这个*不是指对象，而是指全局范围，因为它在函数内部。我见过这种被称为“JavaScript 怪癖”的失败，有些人可能认为这是轻描淡写。:)

```
//Trying to use 'this' without an arrow function

let cat = {
  name: 'Dragon',
  favThings: ['bite', 'scratch', 'destroy'],
  showFavThings: function() {
    this.favThings.forEach(function(fav) {
      alert(this.name + " loves to " + fav + "!");
    });
  }
};

cat.showFavThings();
//output does not include cat name, boooo! 
```

但是我们的朋友，arrow 函数(它是为 JavaScript ES6 开发的，部分原因是前面提到的“怪癖”)，在这里起了作用，因为 *this* 在词汇上绑定到 arrow 函数，这意味着 *this* 将引用 Arrow 函数之前的任何代码。

```
//this with arrow function

let cat = {
  name: 'Dragon',
  favThings: ['bite', 'scratch', 'destroy'],
  showFavThings () {
    this.favThings.forEach((fav) => {
      alert(this.name + " loves to " + fav + "!");
    });
  }
};

cat.showFavThings();
//Dragon loves to bite!
//Dragon loves to scratch!
//Dragon loves to destroy! 
```

干得好，龙！你做你的！

[![Toothless applauds](img/4f5603b6cb9ef3cd6d371cabaa05965e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lecnaJau--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://vignette.wikia.nocookie.net/howtotrainyourdragimg/6/69/Toothless_ecstatic_claps.gif/revision/latest%3Fcb%3D20160224034457)

作为一个编程新手，我仍在学习如何最好地使用箭头功能，因为并不是所有的情况都需要它们。但是你呢？你为什么喜欢或不喜欢这种方法？