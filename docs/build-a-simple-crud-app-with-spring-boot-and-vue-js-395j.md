# 用 Spring Boot 和 Vue.js 构建一个简单的 CRUD 应用程序

> 原文：<https://dev.to/oktadev/build-a-simple-crud-app-with-spring-boot-and-vue-js-395j>

在本教程中，您将构建一个完整的 CRUD web 应用程序，使用 Vue.js 作为客户端，Spring Boot 作为资源服务器。您还将使用 OAuth 2.0 和 Okta 来保护应用程序。

CRUD 是 **C** reate， **R** ead， **U** pdate， **D** elete。这有点像服务器世界的“Hello World”。比如“你好服务器！”如果您可以添加、更新、读取和删除数据，那么您已经拥有了 REST 接口或基本资源 API 的所有基本工具。

您将要构建的示例应用程序是一个简单的 todo 应用程序。通常这些 todo 应用程序使用本地存储的数据，但是在本例中，您将在 Spring Boot 资源服务器上创建、读取、更新和删除 todo。

激动吗？太好了！在我们开始之前，先快速介绍一下相关的技术。

## vue . js 是什么？

Vue 是一个 JavaScript 视图库，像 React 和 Angular。它被设计成可增量采用的，核心库只关注视图层。

根据我的经验，Vue.js 是 React 的一个很好的替代选择。我先学会了 React，后来才开始用 Vue。像 React 一样，Vue 使用虚拟 DOM，提供可反应和可组合的视图组件，并在定义属性和状态时实施严格的单向父子关系。这意味着它是高性能的，并且它避免了在没有单向数据绑定的情况下可能出现的许多令人困惑的状态关系。然而，与 React 不同，Vue 使用模板而不是 JSX(一个潜在的受欢迎且更容易访问的选项),并且 Vue 在单个文件组件中使用`style`标签提供组件范围的 css。在实践中，这种差异相当大，因为在 React 中，JSX 和类 css 的语法与 HTML 和 CSS 非常接近，以至于令人困惑，但实际上并不相同，这在一开始就产生了问题(曾经从不需要分号的语言回到需要分号的语言吗？差不多就是那样)。

我发现 Vue 是一个更简单、更干净的实现。React 需要深入研究。你得吃红色药丸，坚持到底。这是一个超级强大的系统，但你必须全力以赴。Vue 更友好一点，也更容易上手一点。

## 关于 Spring Boot

你要用的服务器技术是 Spring Boot。纯粹的春天(Spring Boot 之前)有点像一个庞然大物:超级强大，但可能会耗费时间和令人沮丧。我很确定整个计算机会议现象的出现是为了让人们能够学习和理解老派的 Spring XML 文件。它当然推动了计算机出版帝国的大部分。

Spring Boot 是 Spring 对这种复杂性(以及像 Ruby on Rails 和 Grails 这样的框架)的回答。他们做了一项伟大的工作，将 Spring 的所有功能浓缩到一个简单、快速、易用的 web 框架中。只需很少的代码行和一些注释，您就可以拥有一个全功能的资源服务器。

另外，当你准备好的时候，f 已经拥有了引擎盖下所有的力量，只等着。

对于这个项目，你需要一个服务器和客户端项目。创建一个名为`SpringBootVueApplication`的项目根目录，并在该目录下创建两个子目录:`client`和`server`。

`client`将是你的 Vue.js 客户端应用。

`server`将成为 Spring Boot 的资源服务器。

## 创建您的 Spring Boot 应用程序

让我们从使用 [Spring 初始化器](https://start.spring.io/)创建 Spring Boot 应用程序开始。

进行以下选择:

*   **项目类型:** `Gradle Project`
*   **组:** `com.okta`
*   **神器:**T0】
*   **依赖关系** : `JPA`，`H2`，`Web`，`Rest Repositories`，`Lombok`

[![Create a new project using the Spring Initializer](../Images/0bce438651aafa6df15964f170c0d8bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2hwik6dJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/spring-initializer-860364a5b5d42c401aa002f3a6f1350f78bfdbcb9eb264f881ba954eacf11e79.png)

下载文件并将内容解压缩到您的`SpringBootVueApplication/server`目录。

首先，让我们从简单的事情开始。把默认端口从 8080 改成 9000(这样就不会和 Vue.js app 客户端端口有一点冲突了)。

将`server/src/main/resources/application.properties`文件的名称改为`application.yml`，并在其中添加以下一行:

```
server:  
  port: 9000 
```

## 定义待办事项模型类

让我们定义 Todo 模型类文件。这定义了您的 Spring Boot 应用程序将使用的数据结构。

在`src/main/java`下的`com.okta.springbootvue`包中创建一个`Todo.java`类。

```
package com.okta.springbootvue;  

import lombok.*;  

import javax.persistence.Id;  
import javax.persistence.GeneratedValue;  
import javax.persistence.Entity;  

@Entity  
@Data  
@NoArgsConstructor  
public class Todo {  

  @Id @GeneratedValue  
  private Long id;  

  @NonNull
  private String title;  

  private Boolean completed = false;

} 
```

这很简单。您正在定义一个具有三个属性的数据模型:一个自动生成的`id`、一个字符串`title`和一个真/假`completed`属性。

Lombok 为您节省了大量定义 getters 和 setters 的冗长仪式代码。这些都是附在课堂上的注释。

后台正在进行大量的工作，允许 Spring Data 和 JPA 自动将这个类文件映射到数据库。这是一个很深的主题，如果你想了解更多，在教程的结尾有链接。现在，知道上面的类将被映射到内存中的 H2 数据库中的一个数据库表，并且该类中的每个属性都将成为一个表列就足够了。默认情况下，您将获得内存中的数据库，其中包含依赖项:H2。这对于教程和测试来说非常方便，但是，显然，对于更多的情况，您会希望包含到实际持久化数据库的映射。

## 定义数据库和 REST 类

在`com.okta.springbootvue`包中创建一个`TodoRepository.java`。

```
package com.okta.springbootvue;  

import org.springframework.data.jpa.repository.JpaRepository;  
import org.springframework.data.rest.core.annotation.RepositoryRestResource;  

@RepositoryRestResource  
interface TodoRepository extends JpaRepository<Todo, Long> {} 
```

这个类非常简单。再说一次，幕后有一大堆事情在进行。我们 Todo 应用程序的 REST API 所需的所有方法实际上都将在这里自动生成。但是，如果需要的话，可以在这个类中定义一些自定义的访问方法。

让我们也在同一个包中创建一个`RestRepositoryConfigurator`类。

```
package com.okta.springbootvue;

import org.springframework.data.rest.core.config.RepositoryRestConfiguration;
import org.springframework.data.rest.webmvc.config.RepositoryRestConfigurer;
import org.springframework.stereotype.Component;

/**
 * IDs are not returned by RestRepository by default. I like them exposed so that the client can easily find
 * the ID of created and listed resources.
 * */
@Component
public class RestRepositoryConfigurator implements RepositoryRestConfigurer {

  @Override
  public void configureRepositoryRestConfiguration(RepositoryRestConfiguration config) {
    config.exposeIdsFor(Todo.class);
  }
} 
```

这个类只是一个配置类。它的全部目的是告诉 Spring 返回带有对象序列化的数据模型实例 ID(这样，您可以通过客户端应用程序的 ID 来引用它们，因为这将是 UUID)。

## 测试 Rest API 服务器

现在，不管你信不信，你已经有了一个可以工作的 REST API。

让我们用 HTTPie 来测试一下。如果没有安装 HTTPie，使用`brew install httpie`安装。或者去他们的网站去实现它。或者只是跟着走。

首先，使用`./gradlew bootRun`启动服务器。

您应该会看到很多输出是这样结束的:

```
2018-11-08 21:20:36.614 INFO 56214 --- [nio-9000-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/] : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-11-08 21:20:36.615 INFO 56214 --- [nio-9000-exec-1] o.s.web.servlet.DispatcherServlet : FrameworkServlet 'dispatcherServlet': initialization started
2018-11-08 21:20:36.646 INFO 56214 --- [nio-9000-exec-1] o.s.web.servlet.DispatcherServlet : FrameworkServlet 'dispatcherServlet': initialization completed in 31 ms
<=========----> 75% EXECUTING [2m 59s]
> :bootRun 
```

现在在服务器端点上执行一个基本的 GET 请求:`http GET http://localhost:9000`

```
HTTP/1.1 200
Content-Type: application/hal+json;charset=UTF-8
Date: Fri, 09 Nov 2018 03:44:37 GMT
Transfer-Encoding: chunked
{
  "_links": {
    "profile": {
      "href": "http://localhost:9000/profile"
    },
    "todos": {
      "href": "http://localhost:9000/todos{?page,size,sort}",
      "templated": true
    }
  }
} 
```

`profile`链接与 ALPS(应用级概要语义)有关。看看上面的[春季文档](https://docs.spring.io/spring-data/rest/docs/current/reference/html/#metadata.alps)。这是一种描述 REST API 公开的可用资源的方式。

`todos`链接是从 Todo 类生成的端点。

使用 GET 请求查看该端点。实际上，您可以省略“GET”和“[http://localhost”](http://localhost%E2%80%9D)，因为这些是 HTTPie 的默认值。

```
$ http :9000/todos
HTTP/1.1 200
Content-Type: application/hal+json;charset=UTF-8
Date: Fri, 09 Nov 2018 03:50:12 GMT
Transfer-Encoding: chunked
{
  "_embedded": {
    "todos": []
  },
  "_links": {
    "profile": {
      "href": "http://localhost:9000/profile/todos"
    },
    "self": {
      "href": "http://localhost:9000/todos{?page,size,sort}",
      "templated": true
    }
  },
  "page": {
    "number": 0,
    "size": 20,
    "totalElements": 0,
    "totalPages": 0
  }
} 
```

`_embedded.todos`保存数据。但是因为还没有 todos，所以它是空的。

您可以使用以下命令向服务器发送一些数据:

`http POST :9000/todos title="Write Vue client app"`

输出将显示您的新待办事项已被添加:

```
HTTP/1.1 201
Content-Type: application/json;charset=UTF-8
Date: Fri, 09 Nov 2018 03:51:22 GMT
Location: http://localhost:9000/todos/1
Transfer-Encoding: chunked
{
    "_links": {
        "self": {
            "href": "http://localhost:9000/todos/1"
        },
        "todo": {
            "href": "http://localhost:9000/todos/1"
        }
    },
    "completed": false,
    "id": 1,
    "title": "Write Vue client app"
} 
```

Todo 已创建！现在，如果您再次获得`/todos`端点，您将看到新创建的 todo。

```
$ http :9000/todos
HTTP/1.1 200
Content-Type: application/hal+json;charset=UTF-8
Date: Fri, 09 Nov 2018 03:54:40 GMT
Transfer-Encoding: chunked
{
"_embedded": {
  "todos": [
    {
      "id": 1,
      "title": "Write Vue client app",
      "completed": false,
      "_links": {
        "self": {
          "href": "http://localhost:9000/todos/1"
        },
        "todo": {
          "href": "http://localhost:9000/todos/1"
        }
      }
    }
  ]
},
...
} 
```

很神奇，是吧？这是用不多的代码实现的大量功能。(以前不是这样的，我来告诉你。我们过去必须在雨中和在 PERL 中使用`vi`两种方式编码，才能让类似的东西工作。你可以用所有的吸气剂和 setters 和仪式代码画一个房子。一小时又一小时。)

## 将 CORS 滤波器添加到您的 Spring Boot 应用程序中

在您继续使用 Vue 客户端应用程序之前，还有一件事需要更新。目前，如果你试图将服务器应用程序用于像 Vue 这样的单页面应用框架，它会抛出一个 CORS 错误。这可以通过在`SpringBootVueApplication`类中添加一个 CORS 过滤器来解决。

什么是 CORS？如果你在问这个问题，请阅读 Spring 的[了解 CORS 文件](https://spring.io/understanding/CORS)。

更新您的`SpringBootVueApplication`类以匹配以下内容。注意在`simpleCorsFilter()`方法中定义的 URL 需要匹配客户端应用程序的 URL。

```
package com.okta.springbootvue;

import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.core.Ordered;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;
import java.util.Collections;
import java.util.stream.Stream;

@SpringBootApplication  
public class SpringBootVueApplication {  

    public static void main(String[] args) {  
      SpringApplication.run(SpringBootVueApplication.class, args);  
    }  

    // Bootstrap some test data into the in-memory database
    @Bean  
    ApplicationRunner init(TodoRepository repository) {  
        return args -> {  
            Stream.of("Buy milk", "Eat pizza", "Write tutorial", "Study Vue.js", "Go kayaking").forEach(name -> {  
                    Todo todo = new Todo();  
                    todo.setTitle(name);  
                    repository.save(todo);  
            });  
            repository.findAll().forEach(System.out::println);  
        };  
    }  

    // Fix the CORS errors
    @Bean
    public FilterRegistrationBean simpleCorsFilter() {  
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();  
        CorsConfiguration config = new CorsConfiguration();  
        config.setAllowCredentials(true); 
        // ***URL below needs to match the Vue client URL and port***
        config.setAllowedOrigins(Collections.singletonList("http://localhost:8080")); 
        config.setAllowedMethods(Collections.singletonList("*"));  
        config.setAllowedHeaders(Collections.singletonList("*"));  
        source.registerCorsConfiguration("/**", config);  
        FilterRegistrationBean bean = new FilterRegistrationBean<>(new CorsFilter(source));
        bean.setOrder(Ordered.HIGHEST_PRECEDENCE);  
        return bean;  
    }   
} 
```

你们中的敏锐者也会注意到`ApplicationRunner init(TodoRepository repository)`功能。这个 bean 是一个初始化钩子，当应用程序运行时，它将一些测试 todos 添加到存储库中。这将使您构建客户端应用程序的过程更加简单。

上客户端 app！

## 安装节点&纱线

本教程假设您已经安装了节点和纱线。如果没有，现在就安装它们。

Yarn 可以用`brew install yarn`安装，或者如果你不是在 mac 上，看看([他们的网站](https://yarnpkg.com/lang/en/docs/install/))。

安装 Node.js 的方法有很多，你可以从他们的网站下载一个版本。我目前使用的是 8.12.0 版本。另一个选项是`n`包管理器。从[他们的 GitHub 页面](https://github.com/tj/n)获取。

## create view . js app

您将使用 Vue CLI 3 从头开始创建一个项目。Vue CLI 是一个很棒的项目，它使构建 Vue 应用程序变得既好又容易。如果你不熟悉，可以看看[他们的网站](https://cli.vuejs.org/)。

使用 yarn 安装 Vue CLI 3:

```
yarn global add @vue/cli@3.1.5 
```

一旦完成，确保您在您的根项目目录`SpringBootVueApplication`中，并运行以下命令:

```
vue create -d client 
```

这将在`client`子目录中创建名为**客户端**的默认 Vue 应用程序。没有`-d`选项，Vue CLI 有一个非常简洁的界面，允许您选择要包含哪些选项。值得下次再去看看。您将要构建的项目基于尤雨溪的[Vue todo MVC 示例项目](https://vuejs.org/v2/examples/todomvc.html)。不同之处在于，这个项目将使用 Spring Boot 服务器而不是浏览器本地存储来持久化 todos。

`cd`进入`SpringBootVueApplication/client`目录。

项目可以用`yarn serve`运行。

现在，您将看到标准的“欢迎使用您的 Vue.js 应用程序”屏幕。

添加几个依赖项:

```
yarn add axios@0.18.0 vuejs-logger@1.5.3 
```

**axios** 是您用来向服务器发出 HTTP 请求的包。 **vuejs-logger** 是一个日志框架，因为你已经不用`console.log()`了，对吗？

添加一个 Vue 配置文件`client/vue.config.js`:

```
module.exports = {
  runtimeCompiler: true
}; 
```

用以下内容替换`src/main.js`

```
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

import VueLogger from 'vuejs-logger';

const options = {
  isEnabled: true,
  logLevel : 'debug',
  stringifyArguments : false,
  showLogLevel : true,
  showMethodName : false,
  separator: '|',
  showConsoleColors: true
};

Vue.use(VueLogger, options);

/* eslint-disable no-new */
new Vue({
  el: '#app',
  template: '<App/>',
  components: { App }
}); 
```

将`src/App.vue`替换为以下内容:

```
<template>
  <div id="app">
    <Todos />
    <footer class="info">
      <p>Based on a project written by <a href="http://evanyou.me">Evan You</a></p>
      <p>Original Vue TodoApp project is <a href="https://vuejs.org/v2/examples/todomvc.html">here</a></p>
      <p>Modified for this tutorial by Andrew Hughes</p>
    </footer>
  </div>
</template>

<script>
  import Todos from './components/Todos'
  // app Vue instance
  const app = {
    name: 'app',
    components: {
      Todos
    },
    // app initial state
    data: () => {
      return {
      }
    }
  }

  export default app
</script>

<style>
  [v-cloak] { display: none; }
</style> 
```

删除`src/components/HelloWorld.vue`模块。如果需要，您也可以删除`src/assets`文件夹，因为您不再需要它。

创建一个名为`src/components/Todos.vue`的新 Vue 组件:

```
<template>
  <div>
    <h1 class="title">Todos</h1>
    <h1 class="email">{{userEmail}}</h1>
    <section class="todoapp">
      <div v-if="loading">
        <h1 class="loading">Loading...</h1>
      </div>
      <div v-else>
        <header class="header">
          <input class="new-todo"
                 autofocus autocomplete="off"
                 :placeholder="this.inputPlaceholder"
                 v-model="newTodo"
                 @keyup.enter="addTodo">
        </header>
        <section class="main" v-show="todos.length" v-cloak>
          <input class="toggle-all" type="checkbox" v-model="allDone">
          <ul class="todo-list">
            <li v-for="todo in filteredTodos"
                class="todo"
                :key="todo.id"
                :class="{ completed: todo.completed, editing: todo == editedTodo }">
              <div class="view">
                <input class="toggle" type="checkbox" v-model="todo.completed" @change="completeTodo(todo)">
                <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
                <button class="destroy" @click="removeTodo(todo)"></button>
              </div>
              <input class="edit" type="text"
                     v-model="todo.title"
                     v-todo-focus="todo == editedTodo"
                     @blur="doneEdit(todo)"
                     @keyup.enter="doneEdit(todo)"
                     @keyup.esc="cancelEdit(todo)">
            </li>
          </ul>
        </section>
        <footer class="footer" v-show="todos.length" v-cloak>
          <span class="todo-count">
            <strong>{{ remaining }}</strong> {{ remaining | pluralize }} left
          </span>
          <ul class="filters">
            <li><a href="#/all" @click="setVisibility('all')" :class="{ selected: visibility == 'all' }">All</a></li>
            <li><a href="#/active" @click="setVisibility('active')" :class="{ selected: visibility == 'active' }">Active</a></li>
            <li><a href="#/completed" @click="setVisibility('completed')" :class="{ selected: visibility == 'completed' }">Completed</a></li>
          </ul>
          <button class="clear-completed" @click="removeCompleted" v-show="todos.length > remaining">
            Clear completed
          </button>
        </footer>
      </div>
    </section>
    <div v-if="error" class="error" @click="handleErrorClick">
      ERROR: {{this.error}}
    </div>
  </div>
</template>

<script>

  // visibility filters
  let filters = {
    all: function (todos) {
      return todos
    },
    active: function (todos) {
      return todos.filter(function (todo) {
        return !todo.completed
      })
    },
    completed: function (todos) {
      return todos.filter(function (todo) {
        return todo.completed
      })
    }
  }

  // app Vue instance
  const Todos = {
    name: 'Todos',
    props: {
      activeUser: Object
    },

    // app initial state
    data: function() {
      return {
        todos: [],
        newTodo: '',
        editedTodo: null,
        visibility: 'all',
        loading: true,
        error: null,
      }
    },

    mounted() {
      // inject some startup data
      this.todos = [{title: 'Drink coffee', completed:false},{title: 'Write REST API', completed:false}];
      // hide the loading message
      this.loading = false;
    },

    // computed properties
    // http://vuejs.org/guide/computed.html
    computed: {
      filteredTodos: function () {
        return filters[this.visibility](this.todos)
      },
      remaining: function () {
        return filters.active(this.todos).length
      },
      allDone: {
        get: function () {
          return this.remaining === 0
        },
        set: function (value) {
          this.todos.forEach(function (todo) {
            todo.completed = value
          })
        }
      },
      userEmail: function () {
        return this.activeUser ? this.activeUser.email : ''
      },
      inputPlaceholder: function () {
        return this.activeUser ? this.activeUser.given_name + ', what needs to be done?' : 'What needs to be done?'
      }
    },

    filters: {
      pluralize: function (n) {
        return n === 1 ? 'item' : 'items'
      }
    },

    // methods that implement data logic.
    // note there's no DOM manipulation here at all.
    methods: {

      addTodo: function () {
        var value = this.newTodo && this.newTodo.trim()
        if (!value) {
          return
        }

        this.todos.push({
          title: value,
          completed: false
        });

        this.newTodo = ''
      },

      setVisibility: function(vis) {
        this.visibility = vis
      },

      completeTodo (todo) {
      },

      removeTodo: function (todo) { // notice NOT using "=>" syntax
        this.todos.splice(this.todos.indexOf(todo), 1)
      },

      editTodo: function (todo) {
        this.beforeEditCache = todo.title
        this.editedTodo = todo
      },

      doneEdit: function (todo) {
        if (!this.editedTodo) {
          return
        }

        this.editedTodo = null
        todo.title = todo.title.trim()

        if (!todo.title) {
          this.removeTodo(todo)
        }
      },

      cancelEdit: function (todo) {
        this.editedTodo = null
        todo.title = this.beforeEditCache
      },

      removeCompleted: function () {
        this.todos = filters.active(this.todos)
      },

      handleErrorClick: function () {
        this.error = null;
      },
    },

    // a custom directive to wait for the DOM to be updated
    // before focusing on the input field.
    // http://vuejs.org/guide/custom-directive.html
    directives: {
      'todo-focus': function (el, binding) {
        if (binding.value) {
          el.focus()
        }
      }
    }
  }

  export default Todos
</script>

<style>
  [v-cloak] { display: none; }
</style> 
```

最后，添加一个名为`public/style.css`的样式表，并将样式表[中的样式复制并粘贴到我们的 GitHub 存储库中。](https://raw.githubusercontent.com/moksamedia/okta-spring-boot-vue/master/client/public/style.css)。

在`public/index.html`中，在`<head></head>`块的底部添加下面一行:隐姓埋名窗口。

```
<link rel="stylesheet" type="text/css" href="<%= BASE_URL %>style.css"> 
```

如果您现在这样做，您将看到一个正常工作的 todo 应用程序，但是数据不会持久化。todos 只是作为数组保存在 Vue 模块中。您将对其进行修改，以便从您的 Spring Boot 资源服务器发送和接收数据。

[![Initial screenshot of the todo app](../Images/e1cd9e441a5f566fb25efa621c004d3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y3IT3h2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/todo-initial-screenshot-95efea42fcd9227ff8d9b1a1040b71f750246151d47f74d789405fcb49d388cc.png)

## 添加客户端逻辑来处理 API 请求

在`client/src`目录下，添加一个文件名`Api.js`，内容如下:

```
import axios from 'axios'  

const SERVER_URL = 'http://localhost:9000';  

const instance = axios.create({  
  baseURL: SERVER_URL,  
  timeout: 1000  
});  

export default {  
  // (C)reate 
  createNew: (text, completed) => instance.post('todos', {title: text, completed: completed}),  
  // (R)ead 
  getAll: () => instance.get('todos', {  
    transformResponse: [function (data) {  
      return data? JSON.parse(data)._embedded.todos : data;  
    }]  
  }),  
  // (U)pdate 
  updateForId: (id, text, completed) => instance.put('todos/'+id, {title: text, completed: completed}),  
  // (D)elete 
  removeForId: (id) => instance.delete('todos/'+id)  
} 
```

这个文件封装了围绕 REST API 请求的一些逻辑。`SERVER_URL`应该是 Spring Boot 服务器的 URL 和端口。

您会注意到定义了 CRUD(创建、读取、更新和删除)函数。除了你设置的`transformResponse`选项之外，这个模块真的很简单。这只是用来规范化`_embedded`响应属性中的数据。

您可能想知道为什么要为这么简单的 API 类费心，认为这些代码可以很容易地放入`Todos`组件中。对本教程来说，的确如此。然而，随着项目的增长，这种封装是保持项目可维护性的关键。

举个例子，假设在未来的某个时刻你决定不想用 axios 模块，或者你的老板告诉你为了`fetch`把它换出来；当您意识到所有代码都方便地集中在一个地方，并且您只需要编辑一个文件(而不是在整个项目中搜索和替换)时，您会觉得非常聪明。

## 从服务器加载数据

现在您需要更改 Todos 组件(`src/components/Todos.vue`)，以便它从 Spring Boot REST 服务器加载数据。

首先要导入您刚刚创建的 Api 模块。在`<template></template>`部分的下面，就在`<script>`标签的下面，添加下面一行:

```
import api from '../Api'; 
```

接下来将`mounted()`方法改为:

```
mounted() {  
  api.getAll()  
    .then(response => {  
      this.$log.debug("Data loaded: ", response.data)  
      this.todos = response.data  
  })  
    .catch(error => {  
      this.$log.debug(error)  
      this.error = "Failed to load todos"  
  })  
    .finally(() => this.loading = false)  
}, 
```

这一更改使用您刚才创建并导入的 Api 模块从 Spring REST 服务加载 todos(而不是简单地在 app 进程中使用数组)。

可以运行`yarn serve`到`http://localhost:8080`。您将看到自举数据正在从 Spring 服务器加载。这假设您的 Spring Boot 应用程序仍在运行。如果没有，使用`./gradlew bootRun`运行它。

[![Screenshot of Todo app with data populated from server](../Images/f8c563b539ce717e9d0853ef5903ee5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0VtWfNLs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/todo-app-screenshot-97b474fa055e7ab9eae9862e891f9c3d210f1a33867fa3f2f0e9b0d2543d38df.png)

当然，您可以编辑这些数据，但这只是编辑本地数组。如果刷新页面，所有编辑内容都会被删除。您仍然需要集成剩余的 CRUD 操作。

## 完成 CRUD 方法

为了完成客户端 CRUD 方法的集成，在`Todos.vue`模块中，更新`methods()`函数以匹配以下内容:

```
methods: {  

  addTodo: function () {  
    var value = this.newTodo && this.newTodo.trim()  
    if (!value) {  
      return  
    }  

    api.createNew(value, false).then( (response) => {  
      this.$log.debug("New item created:", response);  
      this.todos.push({  
        id: response.data.id,  
        title: value,  
        completed: false  
      })  
    }).catch((error) => {  
      this.$log.debug(error);  
      this.error = "Failed to add todo"  
    });  

    this.newTodo = ''  
  },  

  setVisibility: function(vis) {  
    this.visibility = vis  
  },  

  completeTodo (todo) {  
    api.updateForId(todo.id, todo.title, todo.completed).then((response) => {  
      this.$log.info("Item updated:", response.data);  
    }).catch((error) => {  
      this.$log.debug(error)  
      todo.completed = !todo.completed  
      this.error = "Failed to update todo"  
    });  
  },  
  removeTodo: function (todo) { // notice NOT using "=>" syntax 
    api.removeForId(todo.id).then(() => { // notice AM using "=>" syntax 
      this.$log.debug("Item removed:", todo);  
      this.todos.splice(this.todos.indexOf(todo), 1)  
    }).catch((error) => {  
      this.$log.debug(error);  
      this.error = "Failed to remove todo"  
    });
  },  

  editTodo: function (todo) {  
    this.beforeEditCache = todo.title  
    this.editedTodo = todo  
  },  

  doneEdit: function (todo) {  
    if (!this.editedTodo) {  
      return  
    }  
    this.$log.info("Item updated:", todo);  
    api.updateForId(todo.id, todo.title.trim(), todo.completed).then((response) => {
      this.$log.info("Item updated:", response.data);  
      this.editedTodo = null  
      todo.title = todo.title.trim()  
    }).catch((error) => {  
      this.$log.debug(error)  
      this.cancelEdit(todo)  
      this.error = "Failed to update todo"  
    });  

    if (!todo.title) {  
      this.removeTodo(todo)  
    }  
  },  

  cancelEdit: function (todo) {  
    this.editedTodo = null  
    todo.title = this.beforeEditCache  
  },  

  removeCompleted: function () {  
    this.todos = filters.active(this.todos)  
  },  

  handleErrorClick: function () {  
    this.error = null;  
  },  

}, 
```

注意，`methods()`块中定义的方法不使用箭头语法，`=>`。这些函数稍后会被 Vue 绑定到适当的上下文中。在这里使用`=>`将不起作用，因为模块还没有被创建，所以`this`将引用`window`，这不是你想要的。然而，可能令人困惑的是，请注意 API 回调方法*使用了箭头语法。当模块的函数被绑定到模块的`this`实例时，这些箭头函数允许回调引用模块的上下文。*

JavaScript 中绑定`this`的灵活性既是它的优势之一，也是它最令人困惑的方面之一。

享受你的 todo 应用程序的荣耀吧！您仍然需要增加安全性，但除此之外，您已经有了一个功能齐全的 todo 应用程序，它可以在服务器上创建、读取、更新和删除数据。太好了。

教程的这一点对应于存储库的**预授权**分支。

继续使用`yarn serve`运行它。确保您的 Spring Boot 资源服务器仍在运行。

## 集成 Okta 并添加用户认证

Okta 让用户认证变得非常简单。第一步是注册一个免费的 developer.okta.com 账户。接下来，您需要创建一个 OpenID Connect (OIDC)应用程序。登录后，点击**应用**顶部菜单项，然后点击**添加应用**按钮。

[![Create a new Okta Application](../Images/afe9cefccc7733b9287a9da4f4fd29e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zFZ33LM---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/okta-list-apps-f6c265f13034d0a1d678702bb39c21471d7d628c3c8144f9a1ee632c98a6440f.png)

选择**单页 App** 。

[![Use the Okta Application Wizard to create a new app](../Images/a9264e38467b169aae6abe8687161a15.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s0iH0Z_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/okta-app-wizard-9a019a3d912822c873a5e53f3f4c292550b187a3735f6c6e71e64ac5ee6ae7dd.png)

默认的应用程序设置应该没问题。你需要记下你的**客户 ID** ，因为你马上就要用到它。

[![Create a new SPA application](../Images/46346489f32bc80dd1bb2de021bae5ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--779fCYmz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/create-okta-app-31d36e3912bc4e035b58500f270dc9887db88c6a4ace81300a49e9dbfd7dc15c.png)

## 给 Vue 添加认证

Okta 有一个 SDK，可以轻松集成 Vue。使用以下内容安装它:

```
yarn add @okta/okta-vue@1.0.7 
```

现在在客户端应用程序项目中创建一个`src/router.js`文件。

```
import Auth from "@okta/okta-vue";  
import Vue from 'vue'  
import Router from 'vue-router'  
import Todos from './components/Todos'  

Vue.use(Auth, {  
  issuer: 'https://{yourOktaDomain}/oauth2/default',  
  client_id: '{yourClientId}',  
  redirect_uri: window.location.origin + '/implicit/callback',  
  scope: 'openid profile email'  
});  

Vue.use(Router);  

let router = new Router({  
  mode: 'history',  
  routes: [  
    {  
      path: '/',  
      name: 'Todos',  
      component: Todos,  
      meta: {  
        requiresAuth: true  
      }  
    },  
    {  
      path: '/implicit/callback',  
      component: Auth.handleCallback(),  
    },  
  ]  
});  

router.beforeEach(Vue.prototype.$auth.authRedirectGuard());  

export default router; 
```

您需要用您刚刚创建的 OIDC 应用程序中的客户端 ID 替换`{yourClientId}`。您还需要将`{yourOktaDomain}`更改为您的 Okta 预览域，类似于`dev-123456.oktapreview.com`。

Okta Vue 身份验证插件将一个`authClient`对象注入到您的 Vue 实例中，可以通过在您的 Vue 实例中的任何地方调用`this.$auth`来访问该对象。

只有两条路。回家的路线就是 todo app 本身。`meta: { requiresAuth: true } }`属性打开该路由的验证。

另一条路由`/implicit/callback`是 OAuth 2.0 回调路由，它处理来自 Okta 服务器的成功认证。

现在您需要更新`src/main.js`来使用路由器。

将路由器导入文件:

```
import router from './router' 
```

并更新 Vue 应用实例以使用导入的路由器:

```
new Vue({  
  el: '#app',  
  router, // <-- add this line
  template: '<App/>',  
  components: { App }  
}) 
```

接下来，更新`src/App.vue`模块以匹配以下内容:

```
<template>  
  <div id="app">  
  <router-view :activeUser="activeUser"/>  
    <footer class="info">  
      <p v-if="activeUser" class="logout-link"><a @click="handleLogout" href="#">Logout</a></p>  
      <p>Based on a project written by <a href="http://evanyou.me">Evan You</a></p>  
      <p>Original Vue TodoApp project is <a href="https://vuejs.org/v2/examples/todomvc.html">here</a></p>  
      <p>Modified for this tutorial by Andrew Hughes</p>  
    </footer> 
  </div>
</template>  

<script>  
  // app Vue instance 
  const app = {
    name: 'app',  
    // app initial state 
    data: () => {  
      return {  
        activeUser: null  
      }  
    },  

  async created () {  
    await this.refreshActiveUser()  
  },  

  watch: {  
    '$route': 'refreshActiveUser'  
  },  

  methods: {  
    async refreshActiveUser () {  
      this.activeUser = await this.$auth.getUser()  
      this.$log.debug('activeUser',this.activeUser)  
    },  

    async handleLogout () {  
      await this.$auth.logout()  
      await this.refreshActiveUser()  
      this.$router.go('/')  
    }  
  },
}  

export default app  

</script>  

<style>  
  [v-cloak] { display: none; }  
</style> 
```

这些变化证明了一些事情。首先，代码创建并更新属性`activeUser`，该属性将关于当前活动用户的信息传递给 Todos 模块(如果有，则为 null，如果没有)。它还在页脚添加了一个注销按钮。

您需要做的最后一件事是更新`src/Api.js`文件。

```
import axios from 'axios'  
import Vue from 'vue'  

const SERVER_URL = 'http://localhost:9000';  

const instance = axios.create({  
  baseURL: SERVER_URL,  
  timeout: 1000  
});  

export default {  

  async execute(method, resource, data, config) {  
    let accessToken = await Vue.prototype.$auth.getAccessToken()  
    return instance({  
      method:method,  
      url: resource,  
      data,  
      headers: {  
            Authorization: `Bearer ${accessToken}`  
      },  
      ...config  
    })  
  },  

  // (C)reate 
  createNew(text, completed) {  
    return this.execute('POST', 'todos', {title: text, completed: completed})  
  },  
  // (R)ead 
  getAll() {  
    return this.execute('GET','todos', null, {  
      transformResponse: [function (data) {  
        return data? JSON.parse(data)._embedded.todos : data;  
      }]  
    })  
  },  
  // (U)pdate 
  updateForId(id, text, completed) {  
    return this.execute('PUT', 'todos/' + id, { title: text, completed: completed })  
  },  

  // (D)elete 
  removeForId(id) {  
    return this.execute('DELETE', 'todos/'+id)  
  }  
} 
```

这些更改从 Okta Vue Auth 模块获取访问令牌，并将其注入 API 请求方法。

## 试试你的 Vue + Spring Boot 应用

使用`yarn serve`运行应用程序。现在你可以使用 Okta 登录了。当你进入应用程序屏幕时，你会在顶部看到你的电子邮件，在输入占位符中看到你的名字。

**注意:**要看到登录屏幕，您可能需要先退出 developer.okta.com。或者你可以用一个隐蔽的窗口。

[![Okta Login screenshot](../Images/7d00760cd870a54e20de5988bf8307b1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w1CqaKHQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/login-prompt-0ed170acd41de3252ae8286af172f836980a3a6e409f705858244877a98233d0.png)

[![](../Images/0395a1fafd42cd5f6b8e758273d9b1a9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n3x5Scyr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/build-crud-spring-and-vue/todo-app-screenshot-auth-faef0da8b012f501b35ac2f60907c9fd6f57916583a25417cb2e5c896ad9014c.png)

然而，还有一步要走。你可以通过 Okta 登录，但 Spring Boot 服务器应用程序不要求认证。

## 配置 Spring Boot 服务器进行令牌认证

Okta 有一个很好的项目叫做 Okta Spring Boot 启动器([查看 GitHub 项目](https://github.com/okta/okta-spring-boot))，它可以快速、轻松地为 Spring Boot 项目添加令牌认证。

首先，您需要向您的`build.gradle`文件添加几个依赖项。

```
compile ('org.springframework.security.oauth.boot:spring-security-oauth2-autoconfigure:2.0.1.RELEASE')  
compile ('com.okta.spring:okta-spring-boot-starter:0.6.1') 
```

另外，将以下内容添加到 build.gradle 文件的底部。这解决了回退日志记录依赖冲突。

```
configurations.all {  
  exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
  exclude group: 'org.springframework.boot', module: 'logback-classic'  
} 
```

接下来，您需要将以下内容添加到您的`application.yml`文件中，用 Okta OIDC 应用程序中的客户端 ID 替换`{yourClientId}`。

```
okta:  
  oauth2:  
    issuer: https://{yourOktaDomain}/oauth2/default  
    clientId: {yourClientId}  
    scope: openid profile email 
```

最后，您需要向您的`SpringBootVueApplication`类添加`@EnableResourceServer`注释。

```
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableResourceServer;
...

@EnableResourceServer // <- add me
@SpringBootApplication  
public class SpringBootVueApplication {  

   public static void main(String[] args) {  
      SpringApplication.run(SpringBootVueApplication.class, args);  
   }
   ...
} 
```

就是这样！现在，您已经拥有了一个使用 Okta 认证服务的全功能 Vue 客户端和 Spring Boot REST 服务。太容易了。

## 使用 Okta、Vue 和 Spring Boot 做更多事情

这个教程做了不少。您构建了一个 Vue.js 客户端应用程序和一个 Spring Boot REST 服务，并使用它们来演示一个全功能的 CRUD 应用程序。您还使用 Okta 和 Okta Vue SDK 添加了身份验证。

如果你想更深入一点，看看 Okta Vue SDK 项目。

Spring Boot REST 服务使用 Spring Data 的 JPA 实现来基于 Java 类持久化数据。Spring Data 和 JPA 是一个非常深奥的领域，关于它的 Spring 文档是了解更多的好地方。

Okta 还有很多其他很棒的相关教程。

*   [用 Angular 5.0 和 Spring Boot 2.0 构建一个基本的 CRUD 应用](/blog/2017/12/04/basic-crud-angular-and-spring-boot)
*   [用 Vue.js 和节点](/blog/2018/02/15/build-crud-app-vuejs-node)构建一个基本的 CRUD App
*   [用 Spring Boot 和 Spring Security 在 15 分钟内构建一个 Web 应用](/blog/2018/09/26/build-a-spring-boot-webapp)
*   [保护你的 Spring Boot 申请的 10 个好方法](/blog/2018/07/30/10-ways-to-secure-spring-boot)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。