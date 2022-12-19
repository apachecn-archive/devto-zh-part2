# 关于使用 Azure Active Directory B2C (AD B2C)自定义用户界面的补充说明

> 原文：<https://dev.to/muak_x/supplemental-explanation-about-customizing-user-interface-with-azure-active-directory-b2c-ad-b2c-1g6j>

本文补充描述 [Azure Active Directory B2C:自定义 Azure AD B2C 用户界面(UI)](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization) 的文章。

## 在登录页面重新排序社交 ID 提供者

社交提供者的顺序在登录页面上似乎是不变的。虽然我想改变顺序，但相应的 HTML 代码却在不可触及的部分。
所以我用 CSS Flexbox 换了。

```
.options {
  display: flex;
  flex-direction: column;
  div{
    margin-bottom: 15px;
  }
  div:nth-child(4){
    order: -5; // twitter
  }
  div:nth-child(3){
    order: -4; // facebook
  }
  div:nth-child(2){
    order: -3; // google
  }
  div:last-child {
    order: -2; // github
  }
  div:first-child {
    order: 10; // microsoft
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

应用顺序属性，可以更改顺序。
这个例子是，当选择 twitter、facebook、google、GitHub 和微软时。
请注意，根据所选的提供商，原始订单会有所变化。

## 在 iOS Safari 上加载时，登录页面自动向上滚动到邮件表单的问题

尽管我想让社交账户优先于电子邮件账户，但默认情况下这是不可能的。
因为在 iOS Safari 上加载签到页面时，页面会自动向上滚动到电子邮件输入表单。

由于用户界面定制不能使用 Javascript，电子邮件输入焦点不能被控制。

所以我用 CSS 动画强行解决了。

```
.localAccount{
  position: relative;
  animation: fade 1ms ease;
  animation-delay: 1s;
  animation-iteration-count: 1;
  animation-fill-mode: backwards;
  z-index: 10;
}

@keyframes fade {
  0% {
    transform: translateY(-100vh);
    opacity: 0;
  }
  100% {
    transform: translateY(0px);
    opacity: 1;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

电子邮件部分的 localAccount 类向上移动了 1 个屏幕，1 秒钟后将移动到原始位置。这将阻止页面滚动。