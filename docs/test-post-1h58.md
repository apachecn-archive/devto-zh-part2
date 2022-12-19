# 测试岗位

> 原文：<https://dev.to/griffinstewie/test-post-1h58>

[![Gif](img/d73f0e29555b2872ec69ab5c29ce8200.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--17wb7Huy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59q0aj1w9ylmqoe0vgov.gif)T3】

```
extension CGRect {
    public init(center: CGPoint, size: CGSize) {
        origin = CGPoint(x: center.x-size.width/2, y: center.y-size.height/2)
        self.size = size
    }
} 
```

Enter fullscreen mode Exit fullscreen mode