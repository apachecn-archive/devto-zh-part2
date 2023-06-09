# 用 Jest 测试 vue-apollo 组件

> 原文：<https://dev.to/n_tepluhina/testing-vue-apollo-components-with-jest-3gj3>

最近，我一直在用 [vue-apollo](https://github.com/Akryum/vue-apollo) 查询和突变对 Vue 单文件组件进行一些测试。不幸的是，没有太多关于这个话题的指南，所以我决定分享我的经验。这篇文章并不自称是最佳实践，但我希望它能帮助人们开始用 Jest 测试 Vue 中的 [GraphQL](https://www.graphql.com/) + [Apollo](https://www.apollographql.com/) 。

> vue-apollo 是一个在 vue 组件中集成了 apollo 和声明性查询的库

## 项目概述

我将 vue-apollo 测试添加到我的简单的[演示应用程序](https://github.com/NataliaTepluhina/VV-GraphQL-workshop)中。它包含一个`App.vue`组件，一个用于获取 Vue 核心团队成员列表的查询和两个变化:一个用于创建新成员条目，另一个用于删除它。在`apollo-server/schema.graphql`文件中可以找到完整的 GraphQL 模式。

对于组件单元测试，我使用了 [Jest](https://jestjs.io/) 和 [vue-test-utils](https://github.com/vuejs/vue-test-utils) 。

如果你看一下`tests`文件夹，你可能会注意到 project 已经为`App.vue` :
做了一个基本测试

```
import { createLocalVue, shallowMount } from '@vue/test-utils';
import Vuetify from 'vuetify';
import App from '../../src/App';

describe('App', () => {
  let localVue;
  beforeEach(() => {
    localVue = createLocalVue();
    localVue.use(Vuetify, {});
  });

  test('is a Vue instance', () => {
    const wrapper = shallowMount(App, { localVue });
    expect(wrapper.isVueInstance()).toBeTruthy();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个项目使用 Vuetify，所以我将它添加到了`localVue`中，以防止关于其自定义组件的警告。此外，有一个简单的检查，如果组件是一个 Vue 实例。现在是时候写一些 vue-apollo 相关的测试了！

## 简单测试

起初，当我搜索任何关于如何测试 vue-apollo 查询和变异的提示时，我发现了 vue-apollo 作者 Guillaume Chau 的评论

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) 评论为 #244](https://github.com/vuejs/vue-apollo/issues/244#issuecomment-375646919) 

[![Akryum avatar](img/dee04295310bdab7e5dc4315009f66f4.png)](https://github.com/Akryum) **[Akryum](https://github.com/Akryum)** commented on [<time datetime="2018-03-23T12:14:49Z">Mar 23, 2018</time>](https://github.com/vuejs/vue-apollo/issues/244#issuecomment-375646919)

如果你还没有的话，我推荐你使用 [vue 测试工具](https://vue-test-utils.vuejs.org/)。然后你要嘲讽一切和阿波罗有关的东西。如果您有疑问，只需使用`wrapper.setData`。如果你有突变，像这样嘲笑他们:

```
const mutate = jest.fn()
const wrapper = mount(MyComponent, {
  mocks: {
    $apollo: {
      mutate,
    },
  },
})
// ...
expect(mutate).toBeCalled()
```

Enter fullscreen mode Exit fullscreen mode[View on GitHub](https://github.com/vuejs/vue-apollo/issues/244#issuecomment-375646919)

所以我决定使用这个建议开始测试我的组件。让我们创建一个新的测试用例:

```
 test('displayed heroes correctly with query data', () => {
    const wrapper = shallowMount(App, { localVue });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要保存对包装器数据的正确响应，并检查组件是否正确呈现。为了获得响应结构，我们可以检查项目模式中的一个查询:

```
type VueHero {
    id: ID!
    name: String!
    image: String
    github: String
    twitter: String
}

type Query {
  allHeroes: [VueHero]
} 
```

Enter fullscreen mode Exit fullscreen mode

所以`allHeroes`查询应该返回一个由`VueHero`条目组成的数组，并且每个字段类型都被指定。现在很容易模仿我们的包装器中的数据:

```
wrapper.setData({
  allHeroes: [
    {
      id: 'some-id',
      name: 'Evan You',
      image:
        'https://pbs.twimg.com/profile_images/888432310504370176/mhoGA4uj_400x400.jpg',
      twitter: 'youyuxi',
      github: 'yyx990803',
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

厉害了，我们的数据被嘲讽了！现在是时候检查它是否被正确渲染了。为此，我使用了 Jest 快照特性:测试期望组件将匹配给定的快照。最终的测试用例如下所示:

```
test('displayed heroes correctly with query data', () => {
    const wrapper = shallowMount(App, { localVue });
    wrapper.setData({
      allHeroes: [
        {
          id: 'some-id',
          name: 'Evan You',
          image:
            'https://pbs.twimg.com/profile_images/888432310504370176/mhoGA4uj_400x400.jpg',
          twitter: 'youyuxi',
          github: 'yyx990803',
        },
      ],
    });
    expect(wrapper.element).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行它几次，您将会看到测试通过(这并不奇怪，给定的一组数据组件每次都以相同的方式呈现)。这是此刻快照中英雄网格的样子:

```
<v-layout-stub
        class="hero-cards-layout"
        tag="div"
        wrap=""
      >
        <v-flex-stub
          class="hero-cards"
          md3=""
          tag="div"
          xs12=""
        >
          <v-card-stub
            height="100%"
            tag="div"
          >
            <v-card-media-stub
              height="250px"
              src="https://pbs.twimg.com/profile_images/888432310504370176/mhoGA4uj_400x400.jpg"
            />

            <v-card-title-stub
              class="hero-title"
              primarytitle="true"
            >
              <div>
                <h3
                  class="title"
                >
                  Evan You
                </h3>

                <div
                  class="hero-icons"
                >
                  <a
                    href="https://github.com/yyx990803"
                    target="_blank"
                  >
                    <i
                      class="fab fa-github"
                    />
                  </a>

                  <a
                    href="https://twitter.com/youyuxi"
                    target="_blank"
                  >
                    <i
                      class="fab fa-twitter"
                    />
                  </a>
                </div>
              </div>
            </v-card-title-stub>

            <v-card-actions-stub>
              <v-spacer-stub />

              <v-btn-stub
                activeclass="v-btn--active"
                icon="true"
                ripple="true"
                tag="button"
                type="button"
              >
                <v-icon-stub>
                  delete
                </v-icon-stub>
              </v-btn-stub>
            </v-card-actions-stub>
          </v-card-stub>
        </v-flex-stub>
      </v-layout-stub> 
```

Enter fullscreen mode Exit fullscreen mode

让我们现在开始突变测试。我们将检查在我们的 Vue 组件方法`addHero()`中是否调用了`$apollo`方法`mutate`。执行这个检查不需要任何数据，因为我们不期望得到任何结果:我们只是想确定调用了一个突变。在一个新的测试用例中，我们模仿上面注释中所示的`$apollo`，调用`addHero()`方法，然后期望`mutate`被调用:

```
 test('called Apollo mutation in addHero() method', () => {
    const mutate = jest.fn();
    const wrapper = mount(App, {
      localVue,
      mocks: {
        $apollo: {
          mutate,
        },
      },
    });
    wrapper.vm.addHero();
    expect(mutate).toBeCalled();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们对 GraphQL 查询和变异进行了简单的测试。

## 嘲讽 GraphQL 模式

我真的很想看看我的查询在更“真实”的环境中是如何被调用的，我已经在 Apollo docs 的本章中找到了解决方案。这个想法是模仿实际的 GraphQL 模式，并针对它调用查询和变体。

这部分有点复杂，需要做更多的工作，但在我看来，这种测试 GraphQL 调用的方式会给你更精确的结果。让我们从在`tests`文件夹中创建一个新的`mockSchema.js`文件并从`graphql-tools` :
中导入所需的方法开始

```
import { makeExecutableSchema } from 'graphql-tools'; 
```

Enter fullscreen mode Exit fullscreen mode

为了创建一个模式，我简单地从`apollo-server/schema.graphql` :
复制了一个包含所有类型的部分

```
const schema = `
  type VueHero {
    id: ID!
    name: String!
    image: String
    github: String
    twitter: String
  }

  input HeroInput {
    name: String!
    image: String
    github: String
    twitter: String
  }

  type Query {
    allHeroes: [VueHero]
  }

  type Mutation {
    addHero(hero: HeroInput!): VueHero!
    deleteHero(name: String!): Boolean
  } 
`; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用导入的`makeExecutableSchema`方法创建可执行模式。我们应该将我们的模式作为`typeDefs`参数:

```
export default makeExecutableSchema({
  typeDefs: schema,
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们测试需要的另一件事是向模式中添加模拟函数。让我们在我们的`App.spec.js`文件中完成它:

```
import { addMockFunctionsToSchema } from 'graphql-tools';
import schema from '../mockSchema';
...
describe('App', () => {
  let localVue;
  beforeEach(() => {
    localVue = createLocalVue();
    localVue.use(Vuetify, {});
    addMockFunctionsToSchema({
      schema,
    });
  });
  ...
}): 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经准备好测试查询了。

## 用模拟模式测试查询

让我们创建一个新的测试用例，并向其中添加一个查询字符串(如果您不确定查询应该具有什么格式，您可以随时检查您的模式):

```
const query = `
  query {
    allHeroes {
      id
      name
      twitter
      github
      image
    }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们在这里没有使用 Apollo 的`gql`模板文字标签，因为我们将在不包含 Apollo 的情况下进行 GraphQL 调用。我们还将在解决承诺后设置组件数据:

```
graphql(schema, query).then(result => {
  wrapper.setData(result.data);
  expect(wrapper.element).toMatchSnapshot();
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 此时，您可以删除一个先前的快照，并注释/删除一个先前的查询测试用例

整个测试用例应该是这样的:

```
test('called allHeroes query with mocked schema', () => {
    const query = `
      query {
        allHeroes {
          id
          name
          twitter
          github
          image
        }
      }
    `;
    const wrapper = shallowMount(App, { localVue });
    graphql(schema, query).then(result => {
      wrapper.setData(result.data);
      expect(wrapper.element).toMatchSnapshot();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

运行后，如果您检查快照文件，您可能会意识到所有响应字段都等于“Hello World”。为什么会这样？

问题是没有模仿 GraphQL 解析器，我们将总是有一个通用的响应(条目数将总是 2，所有整数将是负数，所有字符串都是`Hello World`)。但是这个通用测试足以检查响应结构。

> GraphQL 解析器是一组特定于应用程序的函数，它们根据模式中描述的查询和变异操作与底层数据存储进行交互。

如果您检查`apollo-server/resolvers`文件，您可以看到真正的解析器正在处理我们数据库中的数据。但是测试环境对数据库一无所知，所以我们也需要模拟解析器。

## 逼真的嘲讽

让我们在`test`文件夹中创建`mockResolvers.js`文件。首先要添加的是`allHeroes`查询的解析器:

```
export default {
  Query: {
    allHeroes: () => [
      {
        id: '-pBE1JAyz',
        name: 'Evan You',
        image:
          'https://pbs.twimg.com/profile_images/888432310504370176/mhoGA4uj_400x400.jpg',
        twitter: 'youyuxi',
        github: 'yyx990803',
      },
    ],
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个查询将总是返回只有一个条目的同一个数组。让我们将解析器添加到`mockSchema.js` :
中的模式

```
import resolvers from './mockResolvers';
...
export default makeExecutableSchema({
  typeDefs: schema,
  resolvers,
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要在测试套件中更改`addMockFunctionsToSchema`调用:为了保持解析器不被模拟数据覆盖，我们需要将`preserveResolvers`属性设置为`true`

```
addMockFunctionsToSchema({
  schema,
  preserveResolvers: true,
}); 
```

Enter fullscreen mode Exit fullscreen mode

删除以前的快照并尝试运行测试。现在，我们可以在新的快照中看到解析器提供的真实数据。

我们还可以添加其他期望，因为现在我们知道一个确切的响应结构。比方说，我们可以检查`allHeroes`数组长度是否等于 1。

本测试用例的最终版本:

```
test('called allHeroes query with mocked schema', () => {
    const query = `
      query {
        allHeroes {
          id
          name
          twitter
          github
          image
        }
      }
    `;
    const wrapper = shallowMount(App, { localVue });
    graphql(schema, query).then(result => {
      wrapper.setData(result.data);
      expect(result.data.allHeroes.length).toEqual(1);
      expect(wrapper.element).toMatchSnapshot();
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 用模拟模式测试突变

现在让我们也用模拟模式测试一个突变。在新的测试用例中创建一个变异字符串常量:

```
test('called Apollo mutation in addHero() method', () => {
    const mutation = `
        mutation {
          addHero(hero: {
            name: "TestName",
            twitter: "TestTwitter",
            github: "TestGithub",
            image: "TestImage",
          }) {
            id
            name
            twitter
            github
            image
          }
        }
    `;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们将传递自定义字符串作为参数，并等待响应。为了定义这个响应，让我们添加一个突变解析器到我们的`mockResolvers`文件:

```
Mutation: {
    addHero: (_, { hero }) => ({
      id: 1,
      name: hero.name,
      image: hero.image || '',
      twitter: hero.twitter || '',
      github: hero.github || '',
    }),
}, 
```

Enter fullscreen mode Exit fullscreen mode

所以我们的`addHero`变异将返回与我们作为其参数传递的英雄完全相同的英雄，并且`id`等于`1`。

现在我们可以向测试用例添加一个 GraphQL 查询:

```
graphql(schema, mutation).then(result => {
  expect(result.data.addHero).toBeDefined();
  expect(result.data.addHero.name).toEqual('TestName');
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们没有检查对 Vue 组件实例的更改，但是可以使用响应随意修改组件数据。

全突变测试用例:

```
test('called addHero mutation with mocked schema', () => {
    const mutation = `
        mutation {
          addHero(hero: {
            name: "TestName",
            twitter: "TestTwitter",
            github: "TestGithub",
            image: "TestImage",
          }) {
            id
            name
            twitter
            github
            image
          }
        }
    `;
    graphql(schema, mutation).then(result => {
      expect(result.data.addHero).toBeDefined();
      expect(result.data.addHero.name).toEqual('TestName');
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测试套件有一个针对`mutate`调用的基本测试和两个带有模拟 GraphQL 模式的‘高级’测试。

如果你想检查所有测试的项目版本，这里有一个 [`testing`分支](https://github.com/NataliaTepluhina/VV-GraphQL-workshop/tree/testing)。