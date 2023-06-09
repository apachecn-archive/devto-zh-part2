# 将谷歌和脸书认证集成到你的 Angular 2+应用程序中

> 原文：<https://dev.to/ram2nitharshan/integrating-google-and-facebook-auth-into-your-angular-2-application-58mg>

我最近不得不面对的最困难的难题之一是在我的一个 Angular 应用程序中包含谷歌和脸书认证。

听起来如此简单的事情也应该很容易实现，对吗？嗯，有一些框架承诺为您简化它。然而，如果你想通过常规方式从谷歌和脸书获得必要的库，事情就变得棘手了。也没有官方版本的打字稿 SDK。所以我想我会让下一个想弄明白的人容易些。

在开始之前，你必须为自己创建一个脸书开发者帐户，并获得一个应用 ID。

要开始将 facebook 登录集成到 Angular 应用程序中，运行以下 npm 命令，

`npm install — save @types/facebook-js-sdk`

在您的 index.html 中，插入以下代码。

`<script type="text/javascript" src="https://connect.facebook.net/en_US/sdk.js"></script>`

在 src 目录中，用下面的代码创建一个 TS 文件。不要忘记在 main.ts 文件中导入这个文件。

`(<any>window).fbAsyncInit = () => {
FB.init({
appId : 'Your App ID',
xfbml : false,
version : 'v2.9'
});
FB.AppEvents.logPageView();
};`

`(function(d, s, id){
let js, fjs = d.getElementsByTagName(s)[0];
if (d.getElementById(id)) { return; }
js = d.createElement(s); js.id = id;
js.src = '//connect.facebook.net/en_US/sdk.js';
fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));`

现在，在您的登录页面中，创建一个您喜欢的脸书按钮，并如下处理点击事件。

`onFacebookButtonClicked() {
FB.getLoginStatus((response) => {
if (response.status === 'connected') {
this.getUserprofileDetails(response.authResponse.accessToken);
} else {
FB.login((loginResponse) => {
this.getUserprofileDetails(loginResponse.authResponse.accessToken);
}, {scope: 'email'});
}
});
}`
T1】

这就是让您的脸书集成工作的基本内容。

还要确保将 Angular 应用程序转换到 https，以保持它始终工作。第一次使用 http 可能不起作用。刷新您的屏幕，然后再试一次，让它在开发测试期间工作。让我们从谷歌认证开始吧。让我感到困惑的是，当 Google 支持 Angular 框架并大力提倡 Typescript 的时候，为什么它没有为此提供一个 Typescript 库。

从这里开始创建一个项目:[https://developers.google.com/identity/sign-in/web/sign-in](https://developers.google.com/identity/sign-in/web/sign-in)。结束时，你应该有自己的客户 ID。

`<meta name="google-signin-scope" content="profile email">
<meta name="google-signin-client_id" content="YourClientID.apps.googleusercontent.com">
<script src="https://apis.google.com/js/platform.js" async defer></script>`

我更喜欢有自己的谷歌登录按钮，以便更好地控制登录流程。

`<button id="googleBtn">
<img src="./../../asseimg/google_signin.png">
</button>
declare const gapi: any;
ngAfterViewInit() {
this.googleInit();
}`
T1】

`public attachSignin(element) {
this.auth2.attachClickHandler(element, {},
(googleUser) => {
const profile = googleUser.getBasicProfile();
this.yourSuccessMethod(profile.getEmail(), profile.getId(), 'Google');
}, (error) => {
console.log(error);
});
}`

瞧。现在你的 Angular 应用程序已经通过了脸书和谷歌的认证。

登录成功后，如果您试图导航到主页，但遇到了没有加载任何主页组件的问题，唯一的解决方法是通过一个标志刷新页面。如果你有任何问题，请在评论区联系我。