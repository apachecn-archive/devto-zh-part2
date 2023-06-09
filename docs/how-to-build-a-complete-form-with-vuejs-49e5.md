# 如何用 Vue.js 构建完整的表单

> 原文：<https://dev.to/teroauralinna/how-to-build-a-complete-form-with-vuejs-49e5>

最近我尝试了一下 [Vue.js JavaScript 框架](https://vuejs.org)。在这篇文章中，我将展示如何用 Vue.js 构建一个几乎功能化的表单。几乎功能化意味着前端已经准备好了，但是后端的实际数据处理还没有实现。

表单几乎是每个 web 应用程序的重要组成部分，所以我想看看如何用下面的规范构建一个基本的表单。

## 规格

我为我的表单项目设定了以下规格。

*   表单包含不同的字段类型。
*   Textarea 字段必须有一个最大长度计数器。
*   表单域必须有内嵌验证。
*   如果验证失败，提交时必须显示验证错误摘要。
*   数据将在提交时通过 Ajax 发送，加载指示器可见，直到 HTTP 请求完成。
*   服务器端错误将以与客户端错误相同的方式返回和处理。
*   发送表单时，必须通过禁用提交按钮来防止双击。
*   成功提交表格后，将显示感谢页面。

## 先决条件

Vue 项目使用 [Webpack 模板](https://github.com/vuejs-templates/webpack)生成。

项目对不属于 Vue.js 核心的功能的依赖性很小:

*   [Vue i18n](https://kazupon.github.io/vue-i18n/en/) 用于内部化；
*   [Vuelidate](https://monterail.github.io/vuelidate/) 进行表单验证；和
*   [Axios HTTP 客户端](https://github.com/axios/axios)用于向后端发出请求。

Project 为表单 HTML 标记使用了 [Bootstrap](https://getbootstrap.com) 4.1.0 库。 [Polyfill.io](https://polyfill.io/v2/docs/) 用于为旧版浏览器提供 Polyfill。

本帖重点关注前端。后端由 [Mocky REST API](https://www.mocky.io) 处理。Mocky 允许模拟 HTTP 响应来测试 REST APIs。当然，您可以使用任何本地模拟服务器，但我决定使用 Mocky，因为它也可以从 GitHub 页面访问。

## 入门

你可以从我的 [GitHub 库](https://github.com/teroauralinna/vue-demo-form)下载整个项目。[现场演示](http://teroauralinna.github.io/vue-demo-form)也有。

```
$ git clone https://github.com/teroauralinna/vue-demo-form.git 
```

Enter fullscreen mode Exit fullscreen mode

### 设置项目

**安装依赖项**

```
$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

### 运行项目

**Serve dev 版本来自[http://localhost:8080](http://localhost:8080)**

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

## 给我看看代码！

### App 设置

#### [T1。/索引. html](#indexhtml)

[从 CDN 中增加了 Bootstrap](https://getbootstrap.com/) 来稍微简化事情。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    Vue.js Demo Form
    <link rel="stylesheet" 
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.0/css/bootstrap.min.css" 
      integrity="sha384-9gVQ4dYFwwWSjIDZnLEWnxCjeSWFphJiwGPXr1jddIhOegiu1FwO5qRGvFXOdJZ4" 
      crossorigin="anonymous">
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
    <script src="https://cdn.polyfill.io/v2/polyfill.min.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。/src/resources/translations.js](#srcresourcestranslationsjs)

这个文件包括我们的应用程序使用的所有翻译字符串。对于更大的应用程序，我会使用每种语言的翻译文件。尽管这个项目没有多种语言，但用翻译字符串处理表单验证错误更容易。

```
export default {
  en: {
    form: {
      firstName: "First name",
      lastName: "Last name",
      email: "Email",
      terms: "I accept the terms and conditions",
      type: "Select subscription type",
      additionalInfo: "Additional info",
      submitted: "The form is submitted!",
      sentInfo: "Here is the info you sent:",
      return: "Return to the form",
      submit: "Submit",
      submitting: "Submitting",
      charactersLeft: "You have {charCount} character left. | You have {charCount} characters left.",
      types: {
        free: "Free trial subscription",
        starter: "Starter subscription (50 € / month)",
        enterprise: "Enterprise subscription (250 € / month)"
      }
    },
    error: {
      invalidFields: "Following fields have an invalid or a missing value:",
      general: "An error happened during submit.",
      generalMessage: "Form sending failed due to technical problems. Try again later.",
      fieldRequired: "{field} is required.",
      fieldInvalid: "{field} is invalid or missing.",
      fieldMaxLength: "{field} maximum characters exceeded."
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。/config/prod.env.js](#configprodenvjs)

在配置文件中，您可以更改 API 端点。一个代表成功，一个代表失败。通过失败响应，您可以看到当出现错误时后端如何响应。

```
'use strict'
module.exports = {
  NODE_ENV: '"production"',
  FORM_API_URL: '"https://www.mocky.io/v2/5adb5a8c2900002b003e3df1"', // Success
  //FORM_API_URL: '"https://www.mocky.io/v2/5ade0bf2300000272b4b29b9"', // Failure
} 
```

Enter fullscreen mode Exit fullscreen mode

**来自[https://www.mocky.io/v2/5adb5a8c2900002b003e3df1](https://www.mocky.io/v2/5adb5a8c2900002b003e3df1):**
的成功响应

```
{  "success":  true,  "errors":  []  } 
```

Enter fullscreen mode Exit fullscreen mode

**来自[https://www.mocky.io/v2/5ade0bf2300000272b4b29b9](https://www.mocky.io/v2/5ade0bf2300000272b4b29b9):**
的失败响应

```
{  "success":  false,  "errors":  [  {  "field":  "firstName",  "message":  null  },  {  "field":  "lastName",  "message":  null  },  {  "field":  "email",  "message":  "Email is not valid email address."  },  {  "field":  "additionalInfo",  "message":  "Max. 1000 characters."  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。/src/main.js](#srcmainjs)

所有依赖关系和一般配置都被插入到`main.js`文件中。

```
import Vue from 'vue'
import VueI18n from 'vue-i18n'
import Vuelidate from 'vuelidate';
import App from './App.vue'
import translations from "./resources/translations";

Vue.use(VueI18n);
Vue.use(Vuelidate);

Vue.config.formApiUrl = process.env.FORM_API_URL;

const i18n = new VueI18n({
  locale: 'en',
  fallbackLocale: 'en',
  messages: translations
})

new Vue({
  el: '#app',
  i18n,
  render: h => h(App)
}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 。/src/App .视图

`App.vue`是应用程序的容器。表单组件被导入到这里。

```
<template>
  <div id="app" class="container">
    <div class="row justify-content-center">
      <div class="col-xs-12 col-sm-10 col-md-6">
        <h1 class="text-center">{{title}}</h1>
        <app-form></app-form>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import Form from './components/form/Form.vue';

export default {
  name: 'app',
  data () {
    return {
      title: 'Vue.js Demo Form'
    }
  },
  components: {
    appForm: Form
  }
}
</script>

<style lang="scss">
  h1 {
    margin-bottom: 30px;
  }

  #app {
    padding: 20px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

### 表单组件

表单组件被分成几个文件:`Form.scss`、`Form.js`和`Form.vue`。后者持有模板并需要 SCSS 和 JS 文件。

#### [T1。/src/components/form/Form.scss](#srccomponentsformformscss)

```
pre {
    white-space: pre-line;
}

form {
    background: #efefef;
    padding: 2rem 2rem 1rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。/src/components/form/Form.js](#srccomponentsformformjs)

`Form.js`包含控制表单的所有逻辑。

```
import { required, email, maxLength } from 'vuelidate/lib/validators';
import axios from 'axios';
import Vue from 'vue';

export default {
  name: 'app-form',
  data() {
    return {
      isSubmitted: false,
      isError: false,
      errorHeader: 'error.invalidFields',
      errors: [],
      types: this.getTypes(),
      submitting: false,
      form: {
        firstName: '',
        lastName: '',
        email: '',
        terms: false,
        type: null,
        additionalInfo: ''
      }
    }
  },
  methods: {
    submit() {
      this.$v.$touch();
      if (!this.$v.$error) {
        this.sendFormData();
      } else {
        this.validationError();
      }
    },
    enableSubmitLoader() {
      this.submitting = true;
    },
    disableSubmitLoader() {
      this.submitting = false;
    },
    sendFormData() {
      this.enableSubmitLoader();
      axios.post(Vue.config.formApiUrl, this.form).then(response => {
        this.submitSuccess(response);
        this.disableSubmitLoader();
      }).catch(error => {
        this.submitError(error);
        this.disableSubmitLoader();
      });
    },
    submitSuccess(response) {
      if (response.data.success) {
        this.isSubmitted = true;
        this.isError = false;
      } else {
        this.errorHeader = 'error.invalidFields';
        this.errors = response.data.errors;
        this.isError = true;
      }
    },
    submitError(error) {
      this.errorHeader = 'error.general';
      this.errors = [{'field': null, 'message': 'error.generalMessage'}];
      this.isError = true;
    },
    validationError() {
      this.errorHeader = 'error.invalidFields';
      this.errors = this.getErrors();
      this.isError = true;
    },
    isErrorField(field) {
      try {
        if (this.getValidationField(field).$error) {
          return true;
        }
      } catch (error) {}

      return this.errors.some(el => el.field === field);
    },
    getErrors() {
      let errors = [];
      for (const field of Object.keys(this.form)) {
        try {
          if (this.getValidationField(field).$error) {
            errors.push({'field': field, 'message': null});
          }
        } catch (error) {}
      }
      return errors;
    },
    getFieldClasses(field) {
      return { 'is-invalid': this.isErrorField(field) }
    },
    getCharactersLeft(field) {
      try {
        return this.getValidationField(field).$params.maxLength.max - this.form[field].length;
      } catch (error) {
        return 0;
      }
    },
    getTypes() {
      return [{
        value: 'free', 
        label: 'form.types.free'
      }, {
        value: 'starter', 
        label: 'form.types.starter'
      }, {
        value: 'enterprise', 
        label: 'form.types.enterprise'
      }];
    },
    getValidationField(field) {
      if (this.$v.form[field]) {
        return this.$v.form[field];
      }
      throw Error('No validation for field ' + field);
    },
    onFieldBlur(field) {
      try {
        this.getValidationField(field).$touch();
        if (this.getValidationField(field).$error) {
          if (!this.errors.some(el => el.field === field)) {
            this.errors.push({'field': field, 'message': null});
          }
        } else {
          this.errors = this.errors.filter(el => el.field !== field);
        }
      } catch (error) {}
    },
    reload() {
      window.location = '';
    }
  },
  validations: {
    form: {
      email: { required, email },
      firstName: { required },
      lastName: { required },
      type: { required },
      terms: { required },
      additionalInfo: { maxLength: maxLength(1000) }
    }
  },
  watch: {
    errors() {
      this.isError = this.errors.length > 0 ? true : false;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。/src/components/表单/Form.vue](#srccomponentsformformvue)

这是包含 HTML 模板，数据绑定和包括 JS 和 SCSS 文件的主要组件。

```
<template>
  <div>
    <form v-if="!isSubmitted" @submit.prevent="submit" novalidate>
      <div class="form-group">
        <label for="firstName">{{ $t('form.firstName') }} *</label>
        <input type="text" class="form-control" id="firstName" v-model.lazy.trim="form.firstName" @blur="onFieldBlur('firstName')" v-bind:class="getFieldClasses('firstName')">
        <div v-if="isErrorField('firstName')" class="invalid-feedback">{{ $t('error.fieldRequired', { field: $t('form.firstName') }) }}</div>
      </div>
      <div class="form-group">
        <label for="lastName">{{ $t('form.lastName') }} *</label>
        <input type="text" class="form-control" id="lastName" v-model.lazy.trim="form.lastName" @blur="onFieldBlur('lastName')" v-bind:class="getFieldClasses('lastName')">
        <div v-if="isErrorField('lastName')" class="invalid-feedback">{{ $t('error.fieldRequired', { field: $t('form.lastName') }) }}</div>
      </div>
      <div class="form-group">
        <label for="email">{{ $t('form.email') }} *</label>
        <input type="email" class="form-control" id="email" v-model.lazy.trim="form.email" @blur="onFieldBlur('email')" v-bind:class="getFieldClasses('email')">
        <div v-if="isErrorField('email')" class="invalid-feedback">{{ $t('error.fieldInvalid', { field: $t('form.email') }) }}</div>
      </div>
      <div class="form-group">
        <label for="type">{{ $t('form.type') }} *</label>
        <select id="type" class="form-control" v-model="form.type" @blur="onFieldBlur('type')" v-bind:class="getFieldClasses('type')">
            <option v-for="type in types" v-bind:key="type.value" v-bind:value="type.value">{{ $t(type.label) }}</option>
        </select>
        <div v-if="isErrorField('type')" class="invalid-feedback">{{ $t('form.type') }}</div>
      </div>
      <div class="form-group">
        <label for="additionalInfo">{{ $t('form.additionalInfo') }}</label>
        <textarea 
          type="additionalInfo" 
          class="form-control" 
          id="additionalInfo" 
          v-model.trim="form.additionalInfo" 
          v-bind:class="getFieldClasses('additionalInfo')" 
          v-bind:maxlength="$v.form['additionalInfo'].$params.maxLength.max" 
          @blur="onFieldBlur('additionalInfo')">
        </textarea>
        <small class="text-muted form-text">{{ $tc('form.charactersLeft', getCharactersLeft('additionalInfo'), { charCount: getCharactersLeft('additionalInfo') }) }}</small>
        <div v-if="isErrorField('additionalInfo')" class="invalid-feedback">{{ $t('error.fieldMaxLength', { field: $t('form.additionalInfo') }) }}</div>
      </div>
      <div class="form-group">
        <div class="form-check">
          <input type="checkbox" class="form-check-input" id="terms" v-model.lazy.trim="form.terms" @change="onFieldBlur('terms')" v-bind:class="getFieldClasses('terms')">
          <label class="form-check-label" for="terms">{{ $t('form.terms') }} *</label>
        </div>
      </div>
      <div class="alert alert-danger" v-if="isError">
        <p class="mb-0">
          <strong>{{ $t(errorHeader) }}</strong>
        </p>
        <ul class="mb-0 pl-3" v-if="errors.length > 0">
          <li v-for="error in errors" v-bind:key="error.field">
            <span v-if="error.field">{{ $t('form.'+error.field) }}<span v-if="error.message">: {{ $t(error.message) }}</span></span>
            <span v-else-if="error.message">{{ $t(error.message) }}</span>
          </li>
        </ul>
      </div>
      <div class="form-group">
        <button type="submit" class="btn btn-primary" :disabled="submitting">
          <span v-if="submitting">{{ $t('form.submitting' ) }} <img src="../../assets/loader.svg" /></span>
          <span v-else>{{ $t('form.submit' ) }}</span>
        </button>
      </div>
    </form>
    <div v-else>
      <div class="alert alert-success">
        <strong>{{ $t('form.submitted' ) }}</strong>
      </div>
      <div class="alert alert-info">
        <p><strong>{{ $t('form.sentInfo' ) }}</strong></p>
        <pre>
            {{form}}
        </pre>
      </div>
      <p class="text-center">
        <a href="#" class="btn btn-secondary" @click.prevent="reload()">{{ $t('form.return' ) }}</a>
      </p>
    </div>
  </div>
</template>

<script src="./Form.js"></script>
<style src="./Form.scss" lang="scss" scoped></style> 
```

Enter fullscreen mode Exit fullscreen mode

## 进一步发展思路

对于一个真实的项目，我会考虑将普通代码从`Form.js`重构为可重用的服务。我还将为每个字段类型创建一个组件，以避免代码重复，并在其他形式中重用字段。另一件吸引我的事情是创建一个代表整个形式的模型。该模型将包括每个表单字段的属性，如类型、标签、验证规则等。然后可以基于表单模型动态生成表单。这可能是一篇即将发表的博文的主题。