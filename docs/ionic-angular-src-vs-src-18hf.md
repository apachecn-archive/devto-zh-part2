# 离子角度 src 与[src]

> 原文：<https://dev.to/_neoky/ionic-angular-src-vs-src-18hf>

直截了当地说，src 不会缓存您的图像[src]会缓存您的图像。因此，如果您想从服务器加载图像资源，并在设备离线时使用它们，请使用[src]=“您的 url 到此处”，而不是 src=“您的 url 到此处”。