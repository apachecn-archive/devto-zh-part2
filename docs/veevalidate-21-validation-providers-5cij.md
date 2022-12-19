# VeeValidate 2.1:验证提供程序

> 原文：<https://dev.to/logaretm/veevalidate-21-validation-providers-5cij>

#### 对 Vue.js 表单进行重考验证

[![](img/1cbd73c5251c291e52e63e2b7be50613.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lWxLVqZn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALLKkfJGsuJsj_AbPUdHjyg.png)

本文标志着 vee-validate 2.1.0 的发布，这是一个基于模板的 Vue.js 验证框架，主要使用指令。

Vue.js 中的指令提供了一些低级别的访问，允许我们操作绑定到指令的元素。

虽然使用起来非常方便，但是指令也有局限性，因为它们没有实例，或者说没有状态。回到 Vue 1.x，他们有状态，允许做更多的事情。这是影响基于模板的 vee-validate 设计的原因之一。

最近，我介绍了 Verify API，它可以在不声明字段的情况下验证值。您甚至可以在**服务器端**使用 verify 方法。🤨

```
import express from 'express';
import bodyParser from 'body-parser';
import { Validator } from 'vee-validate';

const app = express();
app.use(bodyParser.json());
const v = new Validator();

// validate the subscribe request.
function validateReq (req) {
  return v.verify(req.body.email, 'required|email');
}

app.post('/subscribe', async (_req_, _res_) => {
  const { errors, valid } = await validateReq(req);
  if (!valid) {
    return res.status(422).json({
      errors
    });
  }

// ...
});

app.listen(3000); 
```

Enter fullscreen mode Exit fullscreen mode

几周前，这个新的 API 激发了我的一个新想法:

> 我可以使用组件进行验证吗？*🤔*

Vue 有一个名为 [**作用域插槽**](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots) 的组件 API，它允许组件以隔离的方式将数据传递到它的插槽。我在客户的作品中出于各种目的使用了这个特性。

VueApollo 使用它们作为数据提供者，这在某些情况下大大减少了 JavaScript。作用域插槽提供了创建封装在组件中的行为的能力，以及传递结果的方式。

我开始尝试渲染函数，钻研 VNode API。我设法创造了一个可靠的替代指令，我甚至可以说是一个更好的方法。

#### 验证提供者

这是一个利用作用域插槽为输入提供验证的组件，就像 Vuetify 的 VTextField 组件一样。看起来是这样的:

```
<ValidationProvider rules="required">
  <template slot-scope="{ errors }">
    <VTextField v-model="value" :error-messages="errors" />      
  </template>
</ValidationProvider> 
```

Enter fullscreen mode Exit fullscreen mode

除了错误，slot-scope 还包含类、标志和 aria 属性。选择使用这些有用的属性比隐式地将它们注入到组件中要好。它也不会强迫你的模板以某种方式构建。这个 API 是**显式**、**干净**和**灵活**。

```
<ValidationProvider rules="required">
  <template slot-scope="{ errors }">
    <VTextField v-model="foo" :error-messages="errors" />      
  </template>
</ValidationProvider>

<ValidationProvider rules="required">
  <template slot-scope="{ errors }">
    <VTextField v-model="bar" :error-messages="errors" />   
  </template>
</ValidationProvider> 
```

Enter fullscreen mode Exit fullscreen mode

这可能会变得非常冗长，在一个非常大的形式下肯定不好看。

简单的重构会让它更有吸引力。创建由这个组件包装的另一个组件很简单。

```
<template>
  <ValidationProvider _:rules_="rules">
  <template _slot-scope_="{ errors }">
     <VTextField _v-model_="innerValue" _:error-messages_="errors" />   
   </template>
 </ValidationProvider>
</template>

<script>
import { ValidationProvider } from 'vee-validate';

export default {
  name: 'InputWithValidation',
  props: {
    rules: [_String_],
    value: null
  },
  components: {
_ValidationProvider_
},
  data: () => ({
    innerValue: null
  }),
  watch: {
    innerValue (_val_) {
_this_.$emit('input', _val_);
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

重构前一个例子会使它看起来像这样:

```
<InputWithValidation _v-model_="foo" _rules_="required" />

<InputWithValidation _v-model_="bar" _rules_="required" /> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个自我验证的组件，但做得很好。我们还可以使用高阶组件以另一种方式重构它。

#### 高阶分量

高阶函数是接受一个函数并返回一个新函数的函数，一个增强的函数。

同样，高阶组件接受一个组件并返回一个新的增强组件。在我们的例子中，我们希望向组件添加验证。这就是带验证的**的用武之地。** 

```
import { VTextField } from 'vuetify/lib';
import { withValidation } from 'vee-validate';

const VInput = withValidation(VTextField, ({ _errors_ }) => ({
  'error-messages': errors
})); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用新组件来代替您的输入组件。withValidation 函数使用 ValidationProvider 来“增强”您的组件。

第二个参数将插槽范围数据转换为 props，并传递给原始组件。

但是，使用 HoC 有一些缺点，我不会在本文中提及。使用任何一种方法都可以。

使用组件进行验证会带来新的问题。比如在没有注入/共享状态的情况下跟踪当前验证状态🤔。

这里有一个具体的例子:

> 只要输入无效，就禁用按钮。

我们希望能够观察我们的输入，并让某些东西向我们呈现它们的状态。在这一点上，我想为什么不在作用域插槽组件的事情上加倍努力，再增加一个 _ _🤪。

#### 验证观察者

该组件显示子提供程序及其即席变体的当前状态。

看起来是这样的:

```
<ValidationObserver>
  <template _slot-scope_="{ valid }">
    <InputWithValidation _v-model_="foo" _rules_="required" />

    <InputWithValidation _v-model_="bar" _rules_="required" />

    <VBtn _:disabled_="!valid" _@click_="submit">Submit</VBtn>   
  </template>
</ValidationObserver> 
```

Enter fullscreen mode Exit fullscreen mode

您还可以通过按需验证它们或使用 observer 实例上的公共方法重置它们来控制它们。

这是所有正在运行的东西🎉

[https://medium . com/media/545 facb 2525 DD 99 DC c8 DD 888 c 2121 e 72/href](https://medium.com/media/545facb2525dd99dcc8dd888c2121e72/href)

您可以在[文档](https://baianat.github.io/vee-validate/guide/components.html)中找到更详细的描述，包括范围、手动验证和表单重置。_

#### 性能要紧

通过利用 Vue 的虚拟 DOM 和渲染功能，vee-validate 现在能够处理大得离谱的表单。肯定比以前大。

一个[长期存在的问题](https://github.com/baianat/vee-validate/issues/1191)在同一个页面中有 800 个无线电输入会有很差的性能，[另一个使用 120+文本输入的问题](https://github.com/baianat/vee-validate/issues/1593)会导致 Edge 中的内存泄漏。在这两种情况下，使用验证提供程序对性能的影响可以忽略不计。

严格使用这些组件将消除为指令注入状态的全局混合的少量开销。

#### vee validate 的未来

我们已经在 Baianat 中试验了这些组件，到目前为止，它比指令更容易使用，更容易混淆，每个人似乎都喜欢它。

对我来说，这个 API 照亮了 vee-validate 的光明未来。它的 API 比指令小，更可靠，SSR 友好，并且高效💨。

目前，我正在开发一个实验性的分支 v3，它移除了旧的 API，大大减小了包的大小。

*   全包压缩到 34kb(小了 32%)。
*   最小捆绑包减少到 11kb(减少了 57%😲).

一旦 vee-validate 开始向更模块化的方法转变，就可以挤出更多的改进，只提供您在应用中需要/使用的东西，而不是所有东西。

至于 2.x 版本，它们将同时具备这两种方法，并将在现有 API 的基础上继续改进。它将与 3.x 版本具有同等的功能。

我很乐意😍从社区获得反馈。这些变化对你意味着什么？