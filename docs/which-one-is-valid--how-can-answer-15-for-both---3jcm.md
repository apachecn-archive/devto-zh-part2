# 哪个有效？怎么能两个都答 15？

> 原文：<https://dev.to/momin/which-one-is-valid--how-can-answer-15-for-both---3jcm>

### 01

```
 function doSomething(a) {
    b = a + doSomethingElse( a * 2 );

    console.log( b * 3 );
}

function doSomethingElse(a) {
    return a - 1;
}

var b;

doSomething( 2 ); // 15 
```

Enter fullscreen mode Exit fullscreen mode

### 02

```
function doSomething(a) {
    function doSomethingElse(a) {
        return a - 1;
    }

    var b;

    b = a + doSomethingElse( a * 2 );

    console.log( b * 3 );
}

doSomething( 2 ); // 15 
```

Enter fullscreen mode Exit fullscreen mode