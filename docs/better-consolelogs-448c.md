# 更好的控制台.日志

> 原文：<https://dev.to/wangonya/better-consolelogs-448c>

如果您经常使用 javascript，您可能经常需要使用`console.log()`来输出一些信息。

不过通常都是简单的老办法

```
(() => {
    // do stuff
    console.log('Success!')
})() 
```

Enter fullscreen mode Exit fullscreen mode

[![plain](img/21449e81644ccfe5895ffe1940735fc2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Zjmd_28H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7c9pbcb63avm83drprur.png)

这里有一些方法可以让你的日志在视觉上更丰富，更有趣🙂

### 将`console.error()`用于错误日志

```
(() => {
    // do stuff
    console.error('Oops, something went wrong!')
})() 
```

Enter fullscreen mode Exit fullscreen mode

[![errorlog](img/3fd902e7335a3d98d2d325c3a969bac0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UMb97xE1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d2sjc7fbnab0ihih2718.png)

### 将`console.warn()`用于警告日志

```
(() => {
    // do stuff
    console.warn('Warning! Something doesnt seem right.')
})() 
```

Enter fullscreen mode Exit fullscreen mode

[![warninglog](img/712163a8a44b510d2edc6d40a135b19a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dsLIWnaN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5d5je9x3d5kex8y7kosz.png)

### 【编辑】对可迭代对象使用`console.table()`

感谢 [@shoupn](https://dev.to/shoupn) 和 [@squgeim](https://dev.to/squgeim) 在评论中指出这一点:

```
function Person(firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}

const me = new Person('John', 'Smith')

console.table(me) 
```

Enter fullscreen mode Exit fullscreen mode

[![tablelog](img/34ac38cb4b6eedf9f4ac2b59de167347.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HEyIpzqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/smtigum4notzzl2gjrnq.png)

### 添加您的自定义样式

```
(() => {
    // do stuff
    console.log('%c%s',
            'color: green; background: yellow; font-size: 24px;','Success!')
})() 
```

Enter fullscreen mode Exit fullscreen mode

[![custom_success](img/bcfb9b9ac6bad10ab621a9760057237b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pRi5LL-K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ey09anydalwx4jo89nk.png)

你可以在你的代码中有一个自定义函数，让你直接使用“你自己的”带有颜色的日志

```
function customLog(message, color='black') {
     switch (color) {
         case 'success':  
              color = 'Green'
              break
         case 'info':     
                 color = 'Blue'  
              break;
         case 'error':   
              color = 'Red'   
              break;
         case 'warning':  
              color = 'Orange' 
              break;
         default: 
              color = color
     }

     console.log(`%c${message}`, `color:${color}`)
}

customLog('Hello World!')
customLog('Success!', 'success')
customLog('Error!', 'error')
customLog('Warning!', 'warning')
customLog('Info...', 'info') 
```

Enter fullscreen mode Exit fullscreen mode

[![custom_function](img/23829e5b7429bb2e8edf38921fbd4fdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2iOOnh6n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/utfaa6x2ryx37z9540nn.png)

这里是[笔](https://codepen.io/wang0nya/pen/PyRJmO)。

希望您发现这是有用的和愉快的调试！😊