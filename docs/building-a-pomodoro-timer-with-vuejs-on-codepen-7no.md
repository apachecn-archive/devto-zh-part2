# 用 CodePen 上的 Vue.js 构建番茄定时器

> 原文：<https://dev.to/teekatwo/building-a-pomodoro-timer-with-vuejs-on-codepen-7no>

我关注 Scotch.io 上的挑战已经有一段时间了，看到了一个我很感兴趣的挑战。这是一个为 [Scotch.io 挑战#6](https://scotch.io/tutorials/code-challenge-6-build-a-pomodoro-timer) 准备的番茄计时器。总是想测试我的技能，我想试试这个。

## 设置

设置很简单，因为已经有了一个 [codepen](https://codepen.io/sevilayha/pen/QmEEmr) (如下)，所有需要的 html 和 css 工作都已完成。随着主要工作摆在我面前，是时候开始处理这个挑战的 javascript 部分了。

[https://codepen.io/sevilayha/embed/QmEEmr?height=600&default-tab=result&embed-version=2](https://codepen.io/sevilayha/embed/QmEEmr?height=600&default-tab=result&embed-version=2)

这支电笔不能用了

## 第一步

我想做的第一件事是用我需要的所有变量设置我的数据。

```
data: {
    message: 'Let the countdown begin!!',
    timerRunning: false
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是为我的消息创建了一个变量，这个变量将根据计时器的状态以及区分计时器是活动还是暂停的状态而变化。这些状态对于创建我的倒计时方法至关重要。

这些方法很自然地与按钮联系起来。我需要为每个点击时运行的按钮附加一个方法。该要求需要 4 个按钮(开始、暂停、恢复和重置)。

开始按钮将启动倒计时并使`timerRunning: true`开始，因为计时器将开始运行。暂停按钮会冻结倒计时，并使`timerRunning: false`。在制作`timerRunning: true`的时候，恢复按钮会以当前的时间和速度重新开始倒计时。最后，重置按钮会将倒计时设置为开始数，并产生`timerRunning: false`。

这是与我们刚刚谈到的功能相关的方法的原始代码。包括改变某些状态的消息。

```
methods: {
    timerRun() {
      this.timerRunning = true;
      this.message = 'Greatness is within sight!!!';
    },
    timerPause() {
      this.message = 'Never quit, keep going!!';
      this.timerRunning = false;
    },
    timerReset() {
      this.message = 'Let the countdown begin!!';
      this.timerRunning = false;
    },
    timerCountdown() {
      this.timerRunning = true;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了更改某些步骤的消息，我将上面显示的方法与下面显示的按钮绑定在一起，这将触发不同的操作。根据按下的按钮，它可以显示计时器正在运行、计时器暂停、计时器重置或计时器正在运行。随着场景中变量`timerRunning`的改变，也将改变当前显示的按钮配置，具有 v-if 功能。所以，这照顾了按钮的功能，是时候让计时器真正工作了。

```
<div class="buttons">
      <button @click="timerRun" v-if="!timerRunning">Start</button>
      <button @click="timerPause" v-if="timerRunning">Pause</button>
      <button @click="timerReset" v-if="timerRunning">Restart</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

当我去创建计时器时，我意识到我并不真正知道如何编码倒计时，也不了解创建计时器的基本原理。为了了解这应该如何工作，我做了一个时钟。

[https://codepen.io/Vpugh/embed/MVpaxo?height=600&default-tab=result&embed-version=2](https://codepen.io/Vpugh/embed/MVpaxo?height=600&default-tab=result&embed-version=2)

我学习了如何使用毫秒作为所有时钟动作的基础，如何遍历时间，以及显示小时、分钟、秒和毫秒。从这个兼职项目中，我学到了很多关于时间管理的知识。

我在倒数计时时遇到的一个主要问题是不断地穿越时间。当我第一次创建它时，每当在初始启动后按下开始/恢复按钮，倒计时会随着每次按下而递增。这不是预期的结果，也不利于你继续下去。在制作了这个时钟之后，我意识到了一种更加一致的触发计时器启动的方法。

```
data {
    interval: null
},
methods: {
    timerRun() {
      this.timerRunning = true;
      this.message = 'Greatness is within sight!!!';
      this.interval = setInterval(this.countdownTimer, 1000);
    }
    timerPause() {
      this.message = 'Never quit, keep going!!';
      this.timerRunning = false;
      clearInterval(this.interval);
    },
    timerReset() {
      this.message = 'Let the countdown begin!!';
      this.timerRunning = false;
      clearInterval( () => { this.interval; });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码对于从第一次运行到任何后续的恢复都有一个一致的倒计时运动是很重要的。现在，当计时器启动时，一个新的`this.interval`开始倒计时。在暂停和重置时，该变量将被清除，这将暂停倒计时并阻止变量相互叠加。

让计时器倒计时是一条理解大量数学的漫长道路，遗憾的是我对此很不擅长。最后，我需要将时间的解释分解成——小时是 60*60*60，分钟是 60*60，毫秒是 60。所以你需要用毫秒和时间。(如果我解释得不好，我很抱歉，我的数学很糟糕)。

倒数的另一个问题是，如何不进入负数。根据下面的解释，这就是时间不会变成负值的原因(它实际上会变成负值，但我们没有显示出来)。

```
timerCountdown() {
      console.log('Working');
      this.timerRunning = true;
      this.interval = setInterval(this.updateCurrentTime, 1000);
      // Counts down from 60 seconds times 1000.
      setInterval( () => {
        this.timerMinutes--
      }, 60 * 1000)

      // Check if seconds at double zero and then make it a 59 to countdown from.
      // need another method of checking the number while in the loop and then adding a zero on the number under 10
      if(this.timerSeconds === '00'){
        this.timerSeconds = 59;
        setInterval( () => {
          this.timerSeconds--
        }, 1000);
      } else {
        setInterval( () => {
          this.timerSeconds--
        }, 1000);
      }
    }, 
```

Enter fullscreen mode Exit fullscreen mode

> 在他的解决方案中确实有负面影响。你可以包含一个简单的检查，看看时间<= 0 to reset and stop the timer. And how it stays in 60 seconds is just maths. He converts the rounded minutes into seconds and subtracts them from the total time (in seconds). So what will be left are seconds between 0 and 60.
> 是否可以缩短，并使用模来清除。
> `this.totalTime % 60`
> 这样总会留下一个数 0 - 60 的余数。
> 感谢 Zammy13】回答我的问题

[模](https://en.wikipedia.org/wiki/Modulo_operation)的分解(余数%)。

> ![Roel Nieskens profile image](img/883d9f8f30b367d5c8a87bf6ed01e057.png)罗尔·尼斯肯斯@ pixelambacht![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ tee katwo](https://twitter.com/teekatwo)我认了。当我看到“X % Y”时，我通常会想“继续从 X 中减去 Y，直到剩下小于 Y”。
> 
> 为 11 % 3:
> 11 - 3 = 8(好的，8 大于 3，继续)
> 8 - 3 = 5(没错，5 也大于 3，继续)
> 5 - 3 = 2(小于 3，所以“答案”是 2)2018 年 3 月 20 日 19:59[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=976186607794520065)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=976186607794520065)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=976186607794520065)

对于我的计时器，我想要 25 分钟，所以我用这个来代替`totalTime: (25 * 60)`。这等于总时间(25 分钟)乘以 60，等于以秒为单位的时间。总时间是 1500 秒。

```
computed: {
     time: function() {
      return this.minutes + " : " + this.seconds;
    },
    hours: function() {
      var milli = this.milliseconds;
      // var hrs = new Date().getHours();
      // Used getHours() since the below didn't work for me
      var hrs = Math.floor((milli / 3600000) % 24);
      if (hrs >= 13) { hrs = hrs - 12 }
      return hrs >= 10 ? hrs : '0' + hrs;
    },
    minutes: function() {
      var min = Math.floor(this.totalTime / 60);
      return min >= 10 ? min : '0' + min;
    },
    seconds: function() {
      var sec = this.totalTime - (this.minutes * 60);
      return sec >= 10 ? sec : '0' + sec;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是确保你的计时器知道它正在倒计时。这可能是整个事情中最简单的部分，只需检查变量`timerRunning == true`，然后去掉一毫秒。

```
countdownTimer() {
      if (this.timerRunning == true) {
          this.totalTime--;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 结束

这是一条漫长的路，比我想象的要多得多的工作。最后，我做了一些基本的东西，迫不及待地想做一些有各种花哨功能的东西。一些告诉你你在番茄工作法中的位置的东西和一些使它在视觉上更有趣的东西。

挑战的最终密码
[https://codepen.io/Vpugh/embed/VXPrXg?height=600&default-tab=result&embed-version=2](https://codepen.io/Vpugh/embed/VXPrXg?height=600&default-tab=result&embed-version=2)

这个密码笔有很多问题，包括加速倒计时。我在我个人版本的计时器中修复了这个问题。这样做是为了达到一个目标，这就是挑战截止日期。将会有我自己的高级计时器的第二部分。