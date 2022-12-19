# Flexbox 空间-均匀值

> 原文：<https://dev.to/mornir/flexbox-space-evenly-value-58gf>

### 使用 Flexbox 的`space-evenly`值

您肯定已经使用了 Flexbox 属性`justify-content`和`align-items`的`space-around`和`space-evenly`值。

但是你知道你还有一个额外的选择吗？

满足`space-evenly`值。这个“新”值类似于`space-around`,但是在边缘提供相等的而不是一半大小的空间。

 **[![space-around](../Images/6b0dff99d031ba6c003d19ec462074b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kT1WRwRd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/akc15phjho8micpa5f1y.PNG)**

 ****[![space-evenly](../Images/3039e44158b080f9ca6672bc278a5326.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ScPCaYNK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mvur94nr4w0h94odk5q0.PNG)**

 **### 微软 Edge

在微软 Edge 中，`space-evenly`值目前仅在 CSS 网格中受支持。但是这里有一个可能的解决方法，可以回退到`space-around`值:

```
.space-evenly {
  justify-content: space-evenly;
}

@supports (-ms-accelerator: true) {
  /* Edge only */

  .space-evenly {
    justify-content: space-around;
    /* you can also add some other adjustments to size, margins etc to get the same positioning */
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

来源:
[https://developer . Microsoft . com/en-us/Microsoft-edge/platform/issues/15947692/](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/15947692/)
[https://caniuse.com/#search=space-evenly](https://caniuse.com/#search=space-evenly)
[https://cssreference.io/property/justify-content/](https://cssreference.io/property/justify-content/)****