# 在 Vue 中向路由器链路传递数据。射流研究…

> 原文：<https://dev.to/itachiuchiha/passing-data-to-a-router-link-in-vuejs-2cb0>

[![Vue Logo](../Images/2c351b326034e8e2c780cf8489e2b3fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YVmga9rX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xwksyh8ymjr93sh2fgw5.png)

[这篇文章最初发表在我的博客上](https://aligoren.com/passing-data-to-a-router-link-in-vue-js/)

你好。在这篇文章中，我将展示如何在 Vue.JS 中将数据传递到路由器链接。让我们假设我们有一个由 vue-cli 创建的 Vue 项目。我们有一个组件叫做 **HelloWorld** 。默认情况下，有一个名为 HelloWorld 的组件。我们将创建名为**概要文件**的新组件。

例如，您不希望像这样使用查询字符串:

```
https://localhost/#/profile/2 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用参数作为没有查询字符串的道具。我们开始吧。

## 创建 Profile.vue 组件

我将创建一个名为 **Profile.vue** 的 Vue 组件。它会这样:

```
<template>
    <div>
        {{ welcome }}
    </div>
</template>

<script>
export default {
    name: 'Profile',
    props: ['msg'],
    data() {
        return {
            welcome: 'This is your profile'
        }
    },
    mounted() {
        if (this.msg) {
            this.welcome = this.msg    
        }

    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码包含名为 **msg** 的道具，上面的代码返回名为 **welcome** 的对象。当有人直接打开这个页面时应该会看到**这是你的个人资料**消息。如果有人从另一条路过来呢？

## 在 HelloWorld.vue 中修改

让我们考虑一下，由于路由器链接，有人从另一条路线过来。我们的组件应该是这样的:

```
<template>
  <div class="hello">
      <router-link :to="{ name: 'Profile', params: { msg } }">
         Go to your profile
      </router-link>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: `This is Ali's profile`
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们用 msg 对象将它传递给另一个路由。当有人点击到**转到您的个人资料**链接时，页面会重定向到 **[页面。但是当检查 Vue DevTools 时，我们将看不到任何数据。因为我们没有配置路由器文件。](http://localhost:8080/#/profile)**

## 路由器文件的配置

路由器文件应该是这样的:

```
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Profile from '@/components/Profile'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/profile',
      name: 'Profile',
      component: Profile,
      props: true
    }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们看到的，配置文件 route 有 props 键，它的值等于 true。让我们检查 Vue DevTools。

[![Vue Pass Data to Router Link](../Images/46107f927d8380892783eecf2bc346bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dXmVWzZQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h7l4m3fperz2trhfb6aj.png)

如果路由配置是这样的会怎样

```
{
  path: '/profile',
  name: 'Profile',
  component: Profile,
  props: false
} 
```

Enter fullscreen mode Exit fullscreen mode

它不会传递数据。

[![Vue Pass Data to Router Link](../Images/cc8287665b9fa614164b52964b745fbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zjNOPM3y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zy5aajh1nwjm8spnr1ud.png)

感谢您的阅读。希望这篇帖子能帮到你。你可以访问 Vue 路由器[网站](https://router.vuejs.org/)了解更多详情。