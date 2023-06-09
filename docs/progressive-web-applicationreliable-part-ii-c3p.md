# 渐进式网络应用::可靠。第二部分

> 原文：<https://dev.to/stokaboka/progressive-web-applicationreliable-part-ii-c3p>

嗨！

我们继续开发 PWA，并研究如何使用 service worker (SW)。

在第一部分 T0 中，我们:

*   写了自己的服务机；
*   使用 t0work box t1 和 t2work box-web pack-plugin，您可以使用我们的应用程序

## регистрация服务人员

在第一部分中，我们讨论了 t0 APP . js t1 文件，即应用程序的入口点，检查了 SW 的使用情况，并执行了 T2 registerserviceworker 方法

考虑描述此方法的 t0 reg _ SW . js t1 文件。

t0registerviceworker t1t 1 注册 SW:T2 的基本方法

```
/**
 * регистрация service worker (любого)
 * @param serviceWorker - имя фала js являющегося service worker
 * @returns {Promise<void>}
 */
async function registerServiceWorker(serviceWorker) {
    try {
        // регистрируем service worker
        const registration = await navigator.serviceWorker.register(serviceWorker);
        console.log('ServiceWorker registered: ', registration);
        // пытаемся подписаться на PUSH уведомления
        subscribeToPushNotifications(registration);
    } catch (e) {
        // что-то пошло не так
        // скорее всего отсутствует поддержка service worker
        console.error('ServiceWorker failed', e);
    }
} 
```

如果注册成功，SW 将尝试订阅通知推送-subscribetopushnotifications t1 方法:

*   请求访问通知——t0 pushstatus t1 方法
*   我们将尝试订阅通知

```
/**
 * подписка на PUSH сообщения
 * @param registration
 * @returns {Promise<void>}
 */
async function subscribeToPushNotifications(registration) {
    // проверяем возможность работать с PUSH сообщениями
    if ('pushManager' in registration) {
        // опциональные параметры для подписки на Push уведомления
        const options = {
            userVisibleOnly: true,
            applicationServerKey: urlBase64ToUint8Array('BEl62iUYgUivxIkv69yViEuiBIa-Ib9-SkvMeAtA3LFgDzkrxZJjSgSnfckjBJuBkr3qBUYIHBQFLXYp5Nksh8U'),
        };

        // проверяем разрешение от пользователя
        // обрабатывать PUSH уведомления
        const status = await pushStatus;

        if (status) {
            // разрешение на уведомления есть
            // пытаемся подписаться
            try {
                // подписка на PUSH уведомления
                const subscription = await registration.pushManager.subscribe(options);
                console.log('Push registration registered', subscription);
            } catch (e) {
                console.error('Push registration failed', e);
            }
        }
    }
} 
```

在 t0 pushstatus t1 方法中实现了对通知的推送访问。通过 Notifications API T3 中的 notification 接口管理通知。T4￥的

```
const pushStatus = new Promise(resolve => {
    // смотрим права на получение уведомлений,
    // выставленные пользователем
    Notification.requestPermission(result => {
        const el = document.createElement('div');
        el.classList.add('push-info');

        if (result !== 'granted') {
            // уведомления запрещены
            el.classList.add('inactive');
            el.textContent = 'Push blocked';
            resolve(false);
        } else {
            // уведомления разрешены
            el.classList.add('active');
            el.textContent = 'Push active';
            resolve(true);
        }

        document.body.appendChild(el);
    });
}); 
```

> 在登记 SW 和推送通知时，需要考虑一个重要的细节。SW 只能在 t0 local host t1 沙盒中注册，例如在使用 T2 web pack-dev-server T3 时若要在 production 中工作，网页必须已载入 Https，并且必须具有(本身)web 伺服器凭证。

## t0t 1 结论

我们注册了 service worker。这种方法可以用于任何 SW，无论它们是由 workbox 生成的，由我们编写的，还是使用现成的。

### t0t 1 的继续应

t 0t 1 第一部分 t 2:t 3

*   пишем服务人员
*   使用 Workbox 和 workbox-webpack-plugin，您可以在应用程序中使用我们的 service worker

第 III 部:T1

*   服务工作人员
*   缓存策略
*   использование工具箱-网络包-插件модуль生成

### t 0t 1 源

本文所述草案的完整源代码可通过以下链接在 github 上查阅:t0 https://github . com/stokaboka/pwa 01 t1

### t 0t 1 感谢您的关注！

我很高兴听到你的意见和批评。这篇文章是为了学习而写的，以处理 PWA 和 Service Worker 技术。