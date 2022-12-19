# 将 Nuxt.js+Express 项目转换为 TypeScript

> [https://dev . to/dal00/nuxtjsexpresstypescript-5do】的缩写形式](https://dev.to/dala00/nuxtjsexpresstypescript-5doe)

虽然可以很容易地在 create-nuxt-app 中创建 Nuxt.js+Express 项目，但是我们尝试对因此创建的项目进行了 TypeScript 化。

TypeScript 化的对应在 Nuxt.js 端和 Express 端是不同的。 Express 方面有点麻烦，所以我觉得先做 Nuxt.js 什么的也可以。

Nuxt.js+Express 项目的创建使用了以下介绍的 express-template，本文将作为您的前提进行说明。

[在 Nuxt+Express 的项目制作中，看起来好的是什么？](https://crieit.net/posts/Nuxt-Express)

※ 2018/8 现在的信息。

## Nuxt.js 側をTypeScript 化する

基本上，我们参考的是在 create-nuxt-app 的 typescript-template 中实现的内容。

[nuxt-community/Typescript-template:Typescript starter with nuxt . js](https://github.com/nuxt-community/typescript-template)

### 安装 TypeScript 相关

总之先安装 TypeScript。

```
yarn add --dev typescript ts-loader@3.5.0 
```

Enter fullscreen mode Exit fullscreen mode

ts-loader 指定 3.5.0 的版本。 如果没有指定的话，会加入不适合这个项目的版本而出错。

接下来，我们还将放入将 Nuxt.js 和 Vue 与 TypeScript 配合使用的库。

```
yarn add nuxt-property-decorator vue-property-decorator 
```

Enter fullscreen mode Exit fullscreen mode

### 准备设定文件

从刚才的[typescript-template](https://github.com/nuxt-community/typescript-template) 的存储库中直接复制以下文件。

*   索引文件
*   tsconfig.json
*   modules/typescript.js

然后，在 nuxt.config.js 的 modules 中添加`"~/modules/typescript.js"`。

基本上这样就完成了。 接下来，您只需实际将每个组件转换为 TypeScript。

### 对 Vue 组件进行 TypeScript 化

总之是简单组件的样本。 它仍然在 JavaScript 的状态下运行，因此也可以从所需的组件开始依次更改为 TypeScript。

```
<template>
  <div>
    {{ name }}
    <button @click="increment">Click</button>
    {{ cnt }}
    <sub-component></sub-component>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator";
import { Prop } from "vue-property-decorator";
import SubComponent from "./SubComponent.vue";

@Component({
  components: {
    SubComponent
  }
})
export default class extends Vue {
  @Prop({ type: String, required: true })
  name: string;

  cnt = 0;

  increment() {
    this.cnt++;
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

#### scriptタグ

script 标签补充`lang="ts"`。

#### 使用 Vue 类

在 JavaScript 中，您将导出对象，但在 TypeScript 中，您将使用 class，因此您将创建继承了 Vue 类的类并将其导出。

```
export default class extends Vue { 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用组件采集器

对于 TypeScript，请添加`@Component`编译器并在其中添加设置，而不是在类中设置组件。

```
@Component({
  components: {
    SubComponent
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

#### 状态被定义为属性

组件保存的状态不需要使用`data`方法，而是直接定义为类的属性。 当然也可以指定类型。 如果是数组或对象，如果不指定类型，在方法内使用时会发生错误。

```
cnt = 0;
item: Item; 
```

Enter fullscreen mode Exit fullscreen mode

#### 属性使用 Prop

从调用方传递的 Vue 组件的属性使用`@Prop`定义为类的属性。 `@Prop`也可以像`@Prop(Number)`那样，有各种各样的指定方法。

#### 方法是类的方法

其他都差不多。 方法是类的方法，因此不需要用逗号等分隔。

## 快递側をTypeScript 化する

因为 Express 方面有点麻烦，所以心情高涨的话就做，这样的感觉也许也可以。 如果以处理简单的路由为主的话，可能受益也很少。

毕竟开发时和发行构建时的处理也不同，所以需要注意两者都可以移动，同时进行应对。 另外，因为好像有各种各样的方法，所以沉迷于此的人也许还是自己调查一下再安装比较好。 这次先试着做一下，介绍一下动作的方法。

顺便说一下，关于生产环境的步骤，虽然基本上都是在本地进行的，但请注意，由于在实际生产环境中还没有构建，所以可能还会出现什么问题。 一发现什么就追加到等报道中。

### 开发时的设定

使用上述模板，开发时利用 nodemon 启动服务器&实现热加载。 我认为开发的时候在某种程度上适当也没有问题，所以在 nodemon 上可以使用 ts-node。 ts-Node 是一种命令，用于在执行 node 脚本时直接执行 TypeScript。

#### 设置 ts -节点

ts-node 的设定参考了以下内容。

[用 nodejs + TypeScript 开发服务器端时，编辑代码后自动重新加载。](http://aknow2.hatenablog.com/entry/2018/04/09/150006)

首先安装 ts-node。

```
yarn add --dev ts-node 
```

Enter fullscreen mode Exit fullscreen mode

添加配置文件，以便可以在 nodemon 中使用 ts 文件。

nodemon.json

```
{  "watch":  ["server",  "routes"],  "ext":  "ts",  "exec":  "ts-node ./server/index.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

在 package.json 的`dev`脚本中，将以`nodemon server/index.js`表示的部分如`nodemon server/index.ts`所示变更扩展名。

然后把`server/index.js`的扩展名改为 ts 就可以了。

基本上，我想`dev`脚本现在可以运行了，但是如果在其他各文件的全局位置定义了相同的变量( router 等)，就会发生错误，所以要么进行分类，要么更改变量名，要么如下所示将整个文件用大括号括起来

```
{
  const { Router } = require("express");
  const router = Router();

  router.get("/logout", function(req, res) {
    req.logout();
    res.redirect("/");
  });

  app.use("/auth", router);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 其他文件也进行 TypeScript 化

其他路由用文件等也基本上只要将扩展名变更为 ts 就会移动。 从`server/index.ts`读取它们时等，需要指定扩展名。

之后如果使用未定义的变量等，则会被检测为错误，因此如果有未使用的处理等导致错误，请适当修正。

另外，如果中途添加适当的属性，对象也会出错，因此在定义变量时请加上 any 等。

```
const where: any = {
  deleted_at: null
};
where.keyword = req.params.keyword; 
```

Enter fullscreen mode Exit fullscreen mode

### 生产构建用的设定

如果就这样构建的话，会在 ts 文件的旁边大量制作 js 文件，所以要适当地制作文件夹并在那里构建。

tsconfig.json

```
 "outDir":  "./dist" 
```

Enter fullscreen mode Exit fullscreen mode

之后如果在上述文件夹中没有设定文件和 package.json 也会出错，所以要编写脚本进行复制。

copy_statics.js

```
const fs = require("fs-extra");
const targets = ["config", "package.json"];
targets.forEach(path => {
  fs.copySync(path, `dist/${path}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后运行 ts 文件的传输和复制脚本，并修改构建和启动脚本以运行传输的 js 文件。 (从运行构建开始。)

package.json

```
 "build":  "tsc && node copy_statics.js && nuxt build",  "start":  "cross-env NODE_ENV=production node dist/server/index.js", 
```

Enter fullscreen mode Exit fullscreen mode

我认为这样就可以在开发环境、生产环境共同的文件中运行 TypeScript 了。

## 总结

因为只是投稿了在还在开发中的项目中尝试的方法，所以根据情况可能会出现无法顺利运作的模式等。 一有什么发现就再补上。

总之使用未定义的变量时会发生错误，所以减少 typo 的效果好像很快就会出来。