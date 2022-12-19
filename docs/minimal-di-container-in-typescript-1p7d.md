# TypeScript 中的最小 DI 容器

> 原文：<https://dev.to/mindplay/minimal-di-container-in-typescript-1p7d>

我试图想出我能想到的最简单、最有用的异步 DI 容器，结果如下:

```
function Container(provider) {
    const cache = {};

    const container = function (name) {
        if (!cache[name]) {
            cache[name] = provider[name](container);
        }

        return cache[name];
    }

    return container;
} 
```

Enter fullscreen mode Exit fullscreen mode

非常简单——您提供了一个对象(`provider`)，其中每个属性都是一个接收容器并返回解析为服务的承诺的函数。

这里有一个简单的用例:

```
class UserCache { }

class UserService {
    constructor(private cache: UserCache) { }
} 
```

Enter fullscreen mode Exit fullscreen mode

和用法:

```
const container = Container({
    "cache": async c => new UserCache(),
    "user-service": async c => new UserService(await c("cache"))
});

// and a simple test:

container("user-service").then(service => {
    console.log(service);

    container("user-service").then(same_service => {
        console.log(service === same_service); // true
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以[试试这里](https://www.typescriptlang.org/play/index.html#src=function%20Container(provider)%20%7B%0D%0A%20%20%20%20const%20cache%20%3D%20%7B%7D%3B%0D%0A%0D%0A%20%20%20%20const%20container%20%3D%20function%20(name)%20%7B%0D%0A%20%20%20%20%20%20%20%20if%20(!cache%5Bname%5D)%20%7B%0D%0A%20%20%20%20%20%20%20%20%20%20%20%20cache%5Bname%5D%20%3D%20provider%5Bname%5D(container)%3B%0D%0A%20%20%20%20%20%20%20%20%7D%0D%0A%0D%0A%20%20%20%20%20%20%20%20return%20cache%5Bname%5D%3B%0D%0A%20%20%20%20%7D%0D%0A%0D%0A%20%20%20%20return%20container%3B%0D%0A%7D%0D%0A%0D%0A%2F%2F%20EXAMPLE%3A%0D%0A%0D%0Aclass%20UserCache%20%7B%20%7D%0D%0A%0D%0Aclass%20UserService%20%7B%0D%0A%20%20%20%20constructor(private%20cache%3A%20UserCache)%20%7B%20%7D%0D%0A%7D%0D%0A%0D%0Alet%20container%20%3D%20Container(%7B%0D%0A%20%20%20%20%22cache%22%3A%20async%20c%20%3D%3E%20new%20UserCache()%2C%0D%0A%20%20%20%20%22user-service%22%3A%20async%20c%20%3D%3E%20new%20UserService(await%20c(%22cache%22))%0D%0A%7D)%3B%0D%0A%0D%0Acontainer(%22user-service%22).then(service%20%3D%3E%20%7B%0D%0A%20%20%20%20console.log(service)%3B%0D%0A%0D%0A%20%20%20%20container(%22user-service%22).then(same_service%20%3D%3E%20%7B%0D%0A%20%20%20%20%20%20%20%20console.log(service%20%3D%3D%3D%20same_service)%3B%20%2F%2F%20true%0D%0A%20%20%20%20%7D)%3B%0D%0A%7D)%3B%0D%0A)。

这实际上非常强大——例如，您可以像这样引导多个服务提供商:

```
const container = Container({
    ...UserServices,
    ...WebServices,
}); 
```

Enter fullscreen mode Exit fullscreen mode

我不明白的是如何使它成为类型安全的——我知道我需要泛型和`keyof`,但是在这方面我真的需要一些帮助。

有人要吗？:-)