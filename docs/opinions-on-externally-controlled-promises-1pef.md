# 对外部控制承诺的看法？

> 原文：<https://dev.to/hdennen/opinions-on-externally-controlled-promises-1pef>

在我的团队中，当协调一些异步工作时，在外部对象上存储解析函数已经成为一种方便的模式。还有人用这样的承诺吗？也许有一些不可预见的陷阱？

例如，假设我们正在加载图像，并用它们进行一些画布绘制。

```
class CatPichurs {
  constructor() {
    // promise control
    this.cats = {
      felix: {
        promise: new Promise(resolve => this.resolveFelix = resolve)
      },
      cheshire: {
        promise: new Promise(resolve => this.resolveCheshire = resolve)
      },
      garfield: {
        promise: new Promise(resolve => this.resolveGarfield = resolve)
      }
    }

    Promise.all([
      this.cats.felix.promise, 
      this.cats.cheshire.promise, 
      this.cats.garfield.promise
    ])
    .then(allTheCats => {
      allTheCats.forEach(cat => {
        // some big reveal logic
      })
    });
  }

}

class ExternalImageMagic {
  constructor(cats) {
    let felix, cheshire, garfield;

    // load some pictures and draw
    cats.resolveFelix(felix);

    // load more pictures and draw
    cats.resolveCheshire(cheshire);

    // and a couple of Garfield to draw
    cats.resolveGarfield(garfield);
  }
}

const someCats = new CatPichurs();
const imageStuff = new ExternalImageMagic(someCats); 
```

Enter fullscreen mode Exit fullscreen mode

虽然这个例子没有意义，但是您可以看到这个模式是如何方便地将 cat 渲染放在一个地方，而将图像处理放在另一个地方，同时确保两者协调一致。

dev 社区是怎么想的？