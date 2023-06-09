# Firebase 授权的 Vue 模板🔥

> 原文：<https://dev.to/aofdev/vue-templates-for-firebase-auth--30n2>

[![alt tag](img/e6e007ad74425789d2db018e1cc804a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XkBI-_5s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/5AnRW5j.png)

简化 vue 项目的 Firebase 身份验证，支持脸书&谷歌登录& Github & Twitter，并支持渐进式网络应用

## 配置

### 第一步

Firebase Auth 提供方法电子邮件/密码，谷歌，脸书，Github，Twitter 启用 [Firebase 控制台](https://console.firebase.google.com/)

### 第二步

在`main.js`初始化 Firebase[Firebase 凭证](https://console.firebase.google.com/)T3】

```
firebase.initializeApp({
    apiKey: '',
    authDomain: '',
    databaseURL: '',
    projectId: '',
    storageBucket: ''
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 安装

```
# Git Clone Project
git clone git@github.com:aofdev/vue-firebase-auth-vuex.git

# Cd project
cd vue-firebase-auth-vuex

# install dependencies
npm install || yarn install

# serve with hot reload at localhost:8080
npm run dev || yarn dev

# build for production with minification and to build Progressive Web Apps
npm run build || yarn build 
```

Enter fullscreen mode Exit fullscreen mode

**源代码:**[vista-fire base-auth-vuex](https://github.com/aofdev/vue-firebase-auth-vuex)