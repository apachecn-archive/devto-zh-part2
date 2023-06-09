# 渐进式网络应用::可靠。第一部分

> 原文：<https://dev.to/stokaboka/progressive-web-applicationreliable-part-i-4phc>

嗨！

Progressive Web 应用程序(以下简称“PWA”)可以替代基于平台的应用程序。

在资源 t0 developers . Google . com t1 上给出了以下 PWA 定义:

*   **可靠**即时加载，即使在不确定的网络条件下也不会出现宕机。
*   **快速响应**以丝滑流畅的动画快速响应用户交互，没有笨拙的滚动。
*   **Egaging-感觉**像设备上的自然应用程序，具有身临其境的用户体验。

只要事情看起来相当诱人:

*   不同平台的一个代码
*   互联网浏览器作为虚拟机，几乎无处不在
*   丰富的 UI 创建能力
*   能够在不访问广域网的情况下使用应用程序
*   在设备上，PWA“看起来”像一个实物应用程序
*   如果浏览器和 UI 都清楚:实际上，我们只做一页的应用程序，那么离线模式和设备安装怎么办？

选择了几天的空闲时间后，我决定填补这个知识空白，并着手开发最简单的 PWA 应用程序。我需要了解基础知识，然后才能在实际应用程序上使用技术。

> 其主要目的是了解 Reliable 的含义，快速下载应用程序和数据，并能够在无网络的情况下工作。

搜索导致了以下资源:

[https://developers.google.com/web/progressive-web-apps/](https://developers.google.com/web/progressive-web-apps/)
[https://developers.google.com/web/tools/workbox/](https://developers.google.com/web/tools/workbox/)
[https://blog . log rocket . com/building-a-progressive-web-app-pwa-no-react-no-angular-no-vue-aefdded 3 b 5 e](https://blog.logrocket.com/building-a-progressive-web-app-pwa-no-react-no-angular-no-vue-aefdded3b5e)
T9】https://vaa din . com/pwa/build/production-pwa-with-web pack-and-work box

网络上已经有了大量关于 PWA 的文章，每个人都能找到更适合自己的东西。

## t0t 1，那么，我们开始吧！

PWA 与 Single Page Application (SPA)的主要区别在于:安装到设备上、在无网络访问或频繁失去连接的情况下运行，以及从缓存加载应用程序文件和数据。

在使用 ServiceWorker(以下简称 SW)时，这是一种在独立于页面流的流中运行的 JavaScript(以下简称 JS)代码，在 web 应用程序和网络之间充当代理(proxy)。

在这一点上，SW 支持

*   推送通知
*   后台同步

> 但是，实际上，使用 SW 在浏览器中缓存文件并不意味着您必须执行的是 PWA 应用程序。
> 
> 我们可以利用 SW 的文件缓存功能，在本地存储字体、图像和其他 web 应用程序或网页组件，以加快下载速度。

### t 0t 1 任务的设定

开发最简单的 PWA 应用程序，并开发简单的服务工作台，使我们的应用程序在线和离线。

### Решение

我们的应用程序将很简单，但适合进一步发展为更复杂的解决方案。

为此，我们将使用:

*   t0g oogle work box t1j s 库，用于添加对 web 应用程序的独立支持
*   [网络包](https://webpack.js.org/)
*   工作箱网页包插件

请注意，使用 workbox-webpack-plugin 非常方便。在最简单的情况下，只需设置 workbox-webpack-plugin。

Workbox-webpack-plugin 提供了两种在 SW 项目中实施 T1 的方法:

*   t0g eneratesw t1-根据 workbox-webpack-plugin 的最低设置生成 SW 文件。简单地将 SW 集成到应用程序中。但是，请注意，基本默认设置的简单性背后有一个强大的 SW 配置系统，但这是一个单独的主题
*   t0 inject manifest t1-您的或第三方 service worker，完全控制您的设置过程

我们将同时使用这两种选项:GenerateSW 通过 workbox-webpack-plugin 设置来利用 Workbox servce worker 的力量；InjectManifest -编写您最简单的 SW，了解它的工作原理。

### 注射清单

(создадимсвойSW(SW . js)。)[[https://github.com/stokaboka/pwa01/blob/master/src/sw.js](https://github.com/stokaboka/pwa01/blob/master/src/sw.js)

我们将使用 t0 network first t1 策略-如果网络无法从缓存检索数据，如果缓存中没有数据-返回存根数据，则向网络发送请求。

创建辅助变量:

*   API 链接，用于验证网络请求与否(换句话说，我们下载的是什么数据或应用程序组件):

```
 const apiUrl = 'https://jsonplaceholder.typicode.com'; 
```

*   一个数组，列出要存储在缓存中的文件。这些文件的内容将在第一次启动应用程序时保存在缓存中，并由浏览器用于以后启动应用程序(直到缓存过期)，并在没有网络和空缓存的情况下检索数据:

```
 const files = [
        './',
        './app.js',
        './style.css',
        './fallback/posts.json',
        './fallback/users.json',
    ]; 
```

*   解决网络请求(API 访问)与否的辅助方法

```
function isApiCall(req) {
    return req.url.startsWith(apiUrl);
} 
```

首次加载应用程序时，会发生安装过程和缓存初始化。我们截获 install 事件，初始化“files”缓存，并将我们需要的文件存储在缓存中，以便以后使用:﹔t0﹔﹔﹔﹔﹔﹔﹔﹕

```
self.addEventListener('install', async e => {
    // получаем ссылку на кеш
    const cache = await caches.open('files');
    // сохраняем в кеше файлы приложения и заглушки для данных
    cache.addAll(files);
}); 
```

创建处理我们的应用程序请求的基本方法，将有三种:

1)网络查询，以太网方式 T0

```
async function getFromNetwork(req) {
    // ссылка на кеш с тэгом "data"
    const cache = await caches.open('data');

    try {
        // выполняем запрос в сеть
        const res = await fetch(req);
        // сохраняем результат в кеш
        cache.put(req, res.clone());
        return res;
    } catch (e) {
        // упс, что-то пошло не так, сеть не работает!!!
        // извлекаем результат запроса из кеша
        const res = await cache.match(req);
        // возвращаем результат запроса если он найден в кеше
        // возвращаем данные-заглушки 
        // если в кеше нет результатов запроса
        return res || getFallback(req);
    }
} 
```

2)在缓存中查找查询结果，方法为 getfromcache 【t 0

```
async function getFromCache(req) {
    // запрос в кеш
    const res = await caches.match(req);

    if (!res) {
        // в кеше нет данных для запроса
        // отправляем запрос в сеть
        // return fetch(req);
        return getFromNetwork(req)
    }

    return res;
} 
```

3)数据存根，用于无网络和缓存中无数据的情况，getFallback 方法。第一次启动 T0 应用程序时，这些文件已存储在缓存中

```
async function getFallback(req) {
    const path = req.url.substr(apiUrl.length);

    if (path.startsWith('/posts')) {
        return caches.match('./fallback/posts.json');
    } else if (path.startsWith('/users')) {
        return caches.match('./fallback/users.json');
    }
} 
```

现在，我们的 t0 service worker t1 最主要的方法是从我们的 web 应用程序中截取网络请求(浏览器 fetch 调用)

```
self.addEventListener('fetch', async e => {
    // извлекаем запрос из события
    const req = e.request;
    // запрос соответствует нашему api url - обращаемся в сеть
    // прочие запросы (html, css, js, json и любые другие файлы)
    // - пытаемся получить результаты из кеша
    // эти файлы являются частями нашего приложения и
    // сохраняются при первой загрузке
    const res = isApiCall(req) ?
        getFromNetwork(req) : getFromCache(req);
    // подсовываем событию "fetch" результат сформированный нами
    // в вызовах getFromNetwork или getFromCache
    // этот результат будет использован в нашем приложении
    await e.respondWith(res);
}); 
```

我们为 PWA 应用程序编写了自己的第一个 service worker！！！

可以通过以下链接查看完整的文件代码:t0 https://github . com/stoka boka/pwa 01/blob/master/ss

## 【儿基会】*【work box-web pack 插件】**注射清单*

### t0t 1 使用自定义或第三方 service worker。

在我们的案例中，服务工作器是我们的 sw.js 文件

уаклконфигурацииweb pack[web pack . manifest . js](https://github.com/stokaboka/pwa01/blob/master/build-utils/presets/webpack.manifest.js):

```
// загружаем workbox-webpack-plugin
const {InjectManifest} = require('workbox-webpack-plugin');

// настраиваем workbox-webpack-plugin в режиме InjectManifest
module.exports = () => ({
    plugins: [
        new InjectManifest({
            // файл service worker написанный нами
            swSrc: './src/sw.js',
            // под именем service-worker.js наш файл попадет в сборку
            swDest: 'service-worker.js'
        })
    ]
}); 
```

workbox-webpack 外挂程式& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& lt；& gt；& gt；& lt；& gt；& gt；& gt；& gt；& gt；& lt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；& gt；

此文件是 webpack 配置的一部分，它指的是 workbox-webpack-plugin 和 service worker 配置。

> webpack.config.js 本身可通过以下链接访问:t0 https://github . com/stoka boka/pwa 01/

### t0t 1 在 web 应用中的 SW 使用

t0work box-web pack-plugin t 1 将生成两个插件文件:

*   service-worker.js，我们的 sw.js 文件的副本，并导入清单文件(precache-manifest……)。和 workbox-sw.js 软件包
*   预防-管理。五、通过电子邮件、电子邮件、电子邮件。js，清单文件，其中随机字符集

这些文件被复制到部件文件夹中。只剩下将 service worker 连接到我们的 PWA 应用程序。

创建主应用程序文件(入口点)app.js。在此，我们必须:

1.  下载模块，其中包含用于注册 service worker 的方法；
2.  通过调用模块中的 t0 registerserviceworker t1 方法，验证 load 事件浏览器是否可以使用 service worker
3.  下载应用程序的 PWA 数据，并通过调用 api.js 模块中的 t0 loadmoreentries t1 方法显示该数据(适用于应用程序的运行)

```
// загружаем модуль с методами регистрации service worker
import { registerServiceWorker } from './reg_sw'
// методы загрузки данных
import { loadMoreEntries } from "./api";

// имя файла service worker
// этот файл сформирован на основе 
// нашего sw.js workbox-webpack-plugin - ом
// имя файла задано в файле настройке webpack.manifest.js
let serviceWorkerName = '/service-worker.js';

// проверяем возможность обозревателем использовать service worker
if ('serviceWorker' in navigator) {
    // ловим событие "load" - окончание загрузки всех компонентов
    window.addEventListener('load', async () => {
        // регистрируем service worker
        await registerServiceWorker(serviceWorkerName);
        // загружаем данные для работы PWA приложения
        loadMoreEntries();
    });
} 
```

处理 t0 beforeinstall prompt t1 事件浏览器提示您安装 pwa:T2

```
// ловим событие "beforeinstallprompt" - предложение установить
// PWA приложение в системе (при возможности)
// установка возможна только при загрузке по HTTPS
// с сертификатом web-сервера или при работе из песочницы localhost
window.addEventListener('beforeinstallprompt', e => {
    e.preventDefault();
    e.prompt();
}); 
```

## t0t 1 的结论。

因此，我们使用:(WorkBox)[t0 https://developers . Google . com/web 开发了一个测试 PWA 我们用最简单的逻辑设计了自己的 service worker，能够缓存应用程序文件和下载的数据。为了使用我们的 T4 服务机 T5，我们使用了 T6 集成模式 T7。

当网络断开时，我们的应用程序继续运行，显示以前在网络上运行时下载的数据。缓存中不存在的数据请求应用存根数据。这允许应用程序在没有网络访问的情况下继续运行。

### t0t 1 的继续应:

t0t 1 第二部分 T2t 3 第 T4 部分由 servece worker 【t 5 公司注册，并在 push 上签字

T0 第三部分:t1/T2/service worker 的生命周期 T3/缓存策略 T4/使用 workbox

### t0t 1 号机组人员

本文所述草案的完整源代码可通过以下链接在 github 上查阅:t0 https://github . com/stokaboka/pwa 01 t1

谢谢您的关注！

我很高兴听到你的意见和批评。这篇文章是为了学习而写的，以处理 PWA 和 Service Worker 技术。