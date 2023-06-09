# 已发布的笑话匹配器测试工具

> 原文：<https://dev.to/hmsk/published-jest-matcher-vue-test-utils-3af2>

你好，戴夫·托！我听说这里是分享我所做的最好的地方。

通常，我在媒体上写文章。但想知道我是否能搬到德夫.托。

## 主要的东西；

将 Jest + Vue 测试工具的可爱匹配器发布为 [npm 模块](https://www.npmjs.com/package/jest-matcher-vue-test-utils)。你可以通过安装这个 npm 和设置来使用好的匹配器。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[【hmsk】](https://github.com/hmsk)/[【jest-match-view-test-utils】](https://github.com/hmsk/jest-matcher-vue-test-utils)

### ✨可爱的笑话匹配器测试 Vue 组件与 vue 测试工具

<article class="markdown-body entry-content container-lg" itemprop="text">

# jest 匹配器-view-test-utils

[![npm](img/8ecbfa27a6d8f4653c53e21284ca2dbc.png) ](https://www.npmjs.com/package/jest-matcher-vue-test-utils) [ ![GitHub Workflow Status](img/f712aadaf9ae6915e4b74dbaff68fca4.png)](https://github.com/hmsk/jest-matcher-vue-test-utils/actions?query=workflow%3A%22Node+CI%22)

用 [Vue 测试工具](https://vue-test-utils.vuejs.org/)测试 Vue 组件的 [Jest](https://facebook.github.io/jest) 的可爱匹配器。

你可以直观地为 Vue 组件/商店编写测试 <g-emoji class="g-emoji" alias="zap" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a1.png">⚡️</g-emoji>

```
it("Emits 'select' event by clicking PrimaryButton", () => {
  const wrapper = shallowMount(Component);
  expect(wrapper.emitted().select).toBeUndefined();
  wrapper.find(PrimaryButton).vm.$emit("click");
  expect(wrapper.emitted().select[0]).toBeTruthy();
});
```

Enter fullscreen mode Exit fullscreen mode

成为

```
it("Emits 'select' event by clicking PrimaryButton", () => {
  const wrapper = shallowMount(Component);
  expect(() => {
    wrapper.find(PrimaryButton).vm.$emit("click");
  }).toEmit(wrapper, "select");
});
```

Enter fullscreen mode Exit fullscreen mode

还有…

</article>

[View on GitHub](https://github.com/hmsk/jest-matcher-vue-test-utils)

从 npm 获取:

```
$ npm install -D jest-matcher-vue-test-utils 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ yarn install -D jest-matcher-vue-test-utils 
```

Enter fullscreen mode Exit fullscreen mode

然后，在您的 jest 流程中注册匹配者:

```
import vueTestUtilMatchers from "jest-matcher-vue-test-utils";
expect.extend({ ...vueTestUtilMatchers }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以使用:

```
expect(() => wrapper.vm.showError()).toShow(wrapper, "p.error"); 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(() => wrapper.vm.hideError()).toHide(wrapper, "p.error");` 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(Component).toBeValidProps({ name: "required name", fullName: "Kengo Hamasaki" });` 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(Component).toBeValidProp("name", "Required Name");` 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(Component).toRequireProp("name");` 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(Component).toHaveDefaultProp("address", "Kitakyushu, Japan");` 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(Component).toBeValidPropWithTypeCheck("zipcode", "94103");` 
```

Enter fullscreen mode Exit fullscreen mode

```
expect(Component).toBeValidPropWithCustomValidator("fullname", "Kengo Hamasaki");` 
```

Enter fullscreen mode Exit fullscreen mode

目前只有 8 个匹配者，但很高兴听到你的想法，令人讨厌的 Jest + Vue 测试工具！因为我的家是 RSpec，所以任何关于英文的反馈都是非常有用的:)