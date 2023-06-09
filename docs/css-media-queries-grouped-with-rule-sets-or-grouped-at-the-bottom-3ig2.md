# CSS 媒体查询:按规则集分组还是在底部分组？

> 原文：<https://dev.to/jsn1nj4/css-media-queries-grouped-with-rule-sets-or-grouped-at-the-bottom-3ig2>

我使用 CSS 已经有几年了，我总是将我的媒体查询分组在它们所在的任何文件的底部。对我来说，这避免了为相同的屏幕大小(或范围，视情况而定)编写多个媒体查询。

#### 分组媒体查询示例

```
/* general styles */
.selector-1 {
  padding: 10px;
}

.selector-2 {
  font-size: 18px;
}

/* responsive styles */
@media (max-width: 767px) {
  .selector-1 {
    padding: 5px;
  }
}

@media (max-width: 479px) {
  .selector-2 {
    font-size: 14px;
  }
} 
```

我还觉得添加更多的媒体查询会降低性能(除了必须加载更大的 CSS 文件之外)。

不过最近，我已经看到个别媒体查询与相关的选择器组合在一起。

#### 按选择器分组的例子

```
/* .selector-1 styles */
.selector-1 {
  padding: 10px;
}
@media (max-width: 767px) {
  .selector-1 {
    padding: 5px;
  }
}

/* .selector-2 styles */
.selector-2 {
  font-size: 18px;
}
@media (max-width: 479px) {
  .selector-2 {
    font-size: 14px;
  }
} 
```

我确实看到了这样做的立竿见影的好处，但是我想我应该就其中一个或两个问题征求一下意见，以及随着媒体查询数量的增加，是否真的会对性能产生影响。

我还应该提到，我倾向于从事涉及大量 CSS 的项目；我主要在 WordPress 网站上工作，周围有很多预先存在的 CSS。只是随便说说而已。

* * *

为了避免任何混淆，我指的是普通 CSS，而不是像 Sass 或 LESS 这样的预处理器。