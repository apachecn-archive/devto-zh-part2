# Vue.js 的基于布尔玛的 UI 组件

> 原文：<https://dev.to/itachiuchiha/bulma-based-ui-components-for-vuejs-41i4>

[![Buefy Logo](../Images/cfae096b911897146651e9ee42d19398.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0oCvqbOd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jvp07conmiixrbgr758c.png)

[这篇文章最初发表在我的博客上](https://aligoren.com/bulma-based-ui-components-for-vue-js/)

你好。在这篇文章中，我将谈论 [Buefy](https://buefy.github.io/#/) 。如果你在你的项目中使用 Vue.js，你会喜欢 Buefy 的。因为它基于布尔玛框架。[布尔玛](https://bulma.io/)是一个基于 Flexbox 的开源 CSS 框架。我可以说特斯拉在他们的一些[项目](https://auth.tesla.com/login)上使用了布尔玛。如果你厌倦了 Bootstrap，它可能是你的一个好选择。Buefy 中有很多现代的 UI 组件。

## 基于布尔玛的 UI 组件为 Vue.js

开始之前，你需要安装 Vue.js 如你所知。之后，你可以用下面的命令安装 Buefy。

```
npm install buefy 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。我们现在已经安装了 Buefy。现在，我们将对 main.js 文件进行一些配置，以使用 Buefy。首先我们将 Buefy 作为组件和它的 css 文件
导入

```
import Buefy from 'buefy'
import 'buefy/lib/buefy.css' 
```

Enter fullscreen mode Exit fullscreen mode

我们将把 Buefy 组件传递给 vue 的使用方法。例如我们的 main.js 文件应该是这样的:

```
import Vue from 'vue'
import App from './App'
import router from './router'
import Buefy from "buefy"
import 'buefy/lib/buefy.css'

Vue.use(Buefy)

Vue.config.productionTip = false

new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经做了所有使用 Buefy 的事情。让我们从一个简单的例子开始。我将用 Buefy 创建基本导航条组件。

```
<template>
    <div>
        <nav class="navbar is-dark" role="navigation" aria-label="dropdown navigation">
            <div class="navbar-brand">
                <a class="navbar-item not-affect" href="/#/" >
                    <!-- <img :src="logo" alt="Book Reads" width="112" height="28"> -->
                    <span><i class="mdi mdi-24px mdi-home"></i> Home</span>
                </a>
                <div class="navbar-burger burger" v-on:click="showNav = !showNav" v-bind:class="{ 'is-active' : showNav }" data-target="navbarExampleTransparentExample">
                    <span></span>
                    <span></span>
                    <span></span>
                </div>
            </div>
            <div class="navbar-menu" v-bind:class="{ 'is-active' : showNav }">
                <div class="navbar-start">
                    <a class="navbar-item">
                        <span><i class="mdi mdi-24px mdi-library-books"></i> Books</span>
                    </a>
                    <a class="navbar-item">
                        <span><i class="mdi mdi-24px mdi-comment-text-outline"></i> Book Reviews</span>
                    </a>
                    <a class="navbar-item">
                        <span><i class="mdi mdi-24px mdi-account-multiple"></i> Users</span>
                    </a>
                    <div class="navbar-item has-dropdown is-hoverable" @click="showSubMenu()">
                        <a class="navbar-link"><span><i class="mdi mdi-24px mdi-view-list"></i> Books</span></a>
                        <div class="navbar-dropdown is-hidden-touch">
                            <a class="navbar-item" href=""><span><i class="mdi mdi-24px mdi-star"></i> Favorites</span></a>
                        </div>
                    </div>
                </div>
                <div class="navbar-end">
                    <a v-if="navLogin" class="navbar-item" @click="openLogin()">
                        Login
                    </a>
                    <router-link v-if="navLogin" class="navbar-item" to="/register">Signup!</router-link>
                    <!-- <div class="navbar-item ">
                        <div class="field is-grouped">
                            <p class="control">
                                <router-link class="button is-outlined" to="/add-book">
                                    <span>Add New Book</span>
                                </router-link>
                            </p>
                        </div>
                    </div> -->
                </div>
            </div>
        </nav>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

而它的脚本应该是这样的:

```
import LoginModal from "../modals/LoginModal"

export default {
    name: 'Navbar',
    props: ['hideNavLogins'],
    data() {
        return {
            showNav: false,
            logo: './static/logo.png',
            navLogin: true
        }
    },
    components: {
        LoginModal
    },
    mounted() {

        this.navLogin = this.hideNavLogins
        if(this.hideNavLogins == undefined) {
            this.navLogin = true;
        }
    },
    methods: {
        showSubMenu() {

            let m;
            let e;
            try {
                e = event.target;
                m = event.target.nextSibling.nextSibling.classList;

            } catch (error) {
                e = event.target.parentNode;
                m = event.target.parentNode.nextSibling.nextSibling.classList;

            }

            let class1 = e.childNodes[1].classList.contains('mdi-arrow-down') ? 'mdi-arrow-down' : 'mdi-arrow-up';
            let class2 = class1 == 'mdi-arrow-down' ? 'mdi-arrow-up' : 'mdi-arrow-down';

            e.childNodes[1].classList.replace(class1, class2)

            m.toggle('is-hidden-touch')
        },
        openLogin() {
            this.$modal.open({
                parent: this,
                component: LoginModal,
                hasModalCArd: true,
                props: {
                }
            })
        },
        myMethod() {
            console.log("...")
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当您运行您的项目时，它将看起来像这样:

[![Buefy Navbar](../Images/604dbf8ae598ca59fad6c630044e3c8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t8JnbVPv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/trw55y6041rlhss7e1zk.png)

现在，我们将更改默认组件的模板及其脚本部分。例如，我们有一个名为 **Home.vue** 的组件。它的模板应该是这样的:

```
<template>
    <div>
        <navbar></navbar>
        <div class="container is-fullheight hero-body">
            <h3 class="title has-text-dark is-4">Latest Books</h3>
            <b-field grouped>
                <b-input placeholder="isbn:9780136083252 or inauthor:Robert C. Martin intitle:Clean Code or directly Clean Code" v-model="search" type="text" expanded></b-input>
                <p class="control">
                    <button class="button is-primary" @click="searchBooks">Search</button>
                </p>
            </b-field>
            <div class="columns is-centered">
                <div class="column is-12">
                    <div class="box">
                        <section>
                            <div class="columns is-multiline">
                                <div class="column is-4" v-for="book in books" :key="book.id">
                                    <div class="card">
                                        <div class="card-image">
                                            <figure class="image is-4by3">
                                                <img v-bind:src="book.volumeInfo.imageLinks ? book.volumeInfo.imageLinks.thumbnail : ''"  v-bind:alt="book.volumeInfo.title">
                                            </figure>
                                        </div>
                                        <div class="card-content">
                                            <div class="media">
                                                <div class="media-content">
                                                    <p class="title is-6 has-text-danger">{{ book.volumeInfo.title }}</p>
                                                    <strong class="">{{ book.volumeInfo.subtitle }}</strong>
                                                </div>
                                            </div>

                                            <div class="content">
                                                <p>{{ (book.volumeInfo.description) ? book.volumeInfo.description.substring(0,100) + '...' : '' }}</p>
                                            <br>
                                            <time v-bind:datetime="book.volumeInfo.publishedDate">{{ book.volumeInfo.publishedDate }}</time>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </section>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

而它的脚本应该是这样的:

```
import Navbar from "./features/Navbar"

export default {
    name: 'Dashboard',
    data() {
        return {
            search: '',
            desc: '',
            books: []
        }
    },
    components: {
        Navbar
    },
    mounted() {
        this.fetchBooks();
    },
    methods: {
        fetchBooks() {
        },
        async searchBooks() {
            let link = `${this.$api.links['google-search']}${encodeURIComponent(this.search)}&key=AIzaSyAcotR8YZ-Zsd6dcREUBhkUA_NE3UC5AIY`

            const data = await fetch(link).then(resp => resp.json())

            this.books = data.items

        },
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用 google books api。现在，我们的主页应该是这样的:

[![Buefy Home](../Images/b5e28b6a9a2d8578fb4ba71bb40f6c19.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--j09K9Bwq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7l0vxcazchx6xfyyzzuu.png)

## 让我们创建登录模式

我们将创建登录模式。Buefy 有 JavaScript API 来使用[模态](https://buefy.github.io/#/documentation/modal)。在这个例子中，我们将创建一个名为**log in model**的模态组件。

```
<template>
    <div class="modal-card">
        <section class="modal-card-body">
            <login-logo></login-logo>
            <h3 class="title has-text-centered has-text-dark">Member Login</h3>
            <div class="box">
                    <b-field label="E-Mail">
                        <b-input v-model="mail" type="email" placeholder="E-Mail">
                        </b-input>
                    </b-field>

                    <b-field label="Password">
                        <b-input v-model="password" type="password" placeholder="Password" minlength="6" password-reveal>
                        </b-input>
                    </b-field>
                    <b-field>
                        <a class="password-remind-link has-text-dark is-pulled-right" @click="passwordReminder()">I forgot my password</a>
                    </b-field>
                    <button class="button is-dark is-large is-fullwidth" @click="doLogin()">
                        Login
                    </button>
                </div>
                <div class="has-text-centered">
                    <router-link v-on:click.native="closeModal()" to="/register">Signup!</router-link>
                </div>
        </section>
    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

而它的脚本会是这样:

```
import LoginLogo from "../features/LoginLogo"
import PasswordForgot from "./PasswordForgot"

export default {
  name: 'LoginModal',
  data () {
    return {
      mail: '',
      password: '',
    }
  },
  components: {
    LoginLogo,
    PasswordForgot
  },
  methods: {
    passwordReminder() {
        this.$parent.close()
        this.$modal.open({
            parent: this,
            component: PasswordForgot,
            hasModalCArd: true,
            props: {
            }
        })
    },
    closeModal() {
        this.$parent.close()
    },
    doLogin() {
        this.$parent.close()
        this.$router.push('/dashboard')
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的模态将是这样的:

[![Buefy Modal](../Images/0f31fb8bdabf8f6fcf24393c4cc7331c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Slln9aBY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fk7v1mpwz4xq8yf7ypa1.png)

很好。我们创建了基本的 bookreads 门户网站来替代 goodreads。但是布埃菲有更多的成分。例如 jQuery DataTable 的替代表组件。

## 还能做什么？

我们可以创建书评组件。例如，用户将使用 BookReview 组件发送他们的评论。为了访问这个组件，我们可以使用路由机制等。

## 结论

我在 GitLab 上发布了 bookreads 项目。可以直接用也可以叉出来贡献。

[git lab 上的 BookReads 项目](https://gitlab.com/aligoren/bookreads)

在这篇文章中，我们谈到了 Buefy 和 Vue。您可以使用 Buefy 和 Vue 创建伟大的项目。如果你有任何问题，请提问

感谢阅读。