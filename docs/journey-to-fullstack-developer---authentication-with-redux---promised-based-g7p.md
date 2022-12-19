# 基于 Redux - promise 的全栈开发人员认证之旅。

> 原文：<https://dev.to/legobox/journey-to-fullstack-developer---authentication-with-redux---promised-based-g7p>

> 这篇文章最初发表在我的博客上，地址是 [legobox](https://medium.com/legobox/journey-to-fullstack-developer-authentication-with-redux-promised-based-cc17e7a08425)

## 到目前为止的周数。

在投标中，坦率地说，我将写关于我自己的懒惰，是的，我真的对这个项目很懒。我有一个通常不能完成激情项目的问题，但我正试图改变这一点，这也是为什么我不得不写下我的懒惰。为了让大家了解我的全部经历。有时候我真的觉得我不太喜欢编码，如果有更简单的方法来解决问题，我可能会选择它。

到目前为止，spectre 一直进展缓慢，这要感谢我(这里是讽刺)，但我肯定会改善这一点。本周完成了所有与认证相关的程序，我有机会在基于 react 的项目中好好看看认证，并对该过程有了一些概述，我想我很乐意分享。

## 构建基于身份验证-冗余

确实有很多关于身份认证的文章，因为随之而来的是与过程相关的许多特质，很多时候没有足够的解释来处理一些场景，在其他情况下，这些想法还没有准备好投入生产。

一方面，我们的文章很好地描述了它将如何实现，但未能说明如何最好地设置系统，以便它能够跟踪与服务器提供的令牌状态相关的身份验证状态。也就是说，如果应用程序的状态显示该用户已登录，但令牌已过期，以及如果情况相反，该怎么办。见鬼，还有其他事情要考虑。

*   重定向发生在组件中还是动作中？
*   我们如何处理来自动作的异步操作？
*   使用 redux 的最佳实践是什么？

在这个过程的最后，我得出了以下结论，

1.  承诺是处理基于 redux 的异步动作的方法，它们可能是所有基于 redux 的动作中最好的。
2.  动作意味着只关注执行与服务器通信和改变状态的动作，而不是别的。
3.  路线更改必须只发生在组件中，而不发生在其他地方。
4.  所有的 API 动作都必须由 axios 支持的 API 类来管理。因为它是基于承诺的，所以它直接适合于系统的基于承诺的架构。
5.  因为我们异步处理所有事情，所以尽可能使用 async-await 模式以获得更直观的语法。

### 记号与状态的关系。

在监视状态的过程中，有两个主要属性决定用户是否登录。

*   从服务器接收的令牌旨在用作访问令牌。
*   isAuthenticated state 属性的状态 true 或 false。此属性依赖于令牌属性。

使用这两种方法，在进行身份验证检查时需要监控一些组合。

*   **isAuthenticated = true** ，找到令牌(用户实际登录)
*   **isAuthenticated = false** ，找到了令牌(用户已登录，但由于某种原因，状态被打乱)
*   **isAuthenticated = true** ，令牌过期(用户不再登录服务器，因此我们必须通信)
*   **isAuthenticated = false** ，令牌过期(用户真正注销)。

我以前写过一篇关于这种结构的文章，它解释了细节，但在这个过程中，我改变了那篇文章的一些内容，我想我必须更新它，但这里有一些事情要注意，如果你没有读过那篇文章，有一个链接。它会帮助你理解这里发生了什么。

从那篇文章中，有 3 个主要组件处理路由。

*   **AppCheck 容器**——负责更新路由，以记录用户是登录还是注销，从而进行适当的重定向。
*   **EnsureLoggedInContainer**-确保访问这些路由的用户通过了身份验证
*   **EnsureVisitorContainer** -确保访问这些路线的用户是访问者(未登录)。

好了，废话不多说，下面是我对这些组件的修改建议。

在 AppCheck 组件上，我添加了一个 componentDidMount 生命周期挂钩来检查与状态相关的令牌的存在，并对状态进行适当的更改。

```
componentDidMount(){
    const { currentURL, pages, dispatch, isLoggedIn } = this.props;

    // note isLoggedIn is mapped directly to isAuthenticated by redux connect

    let token = this.props.cookies.get('uat');
    console.log(token);
    switch (isLoggedIn){
      case true: 
        if(!token){
          dispatch(actions.logout()); // this action automatically changes the state of isAuthenticated to false
        }
        break
      case false:
        if(token){
          dispatch(actions.autoLogin()); // this action automatically changes the state of isAuthenticated to true
        }
        break;
      default:
        break;
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

显然，这是我需要对 AppCheck 上的检查进行的唯一更改，其他检查(EnsureLoggedInContainer 和 EnsureVisitorContainer)基本保持不变。

### 用承诺还原设置。

在这个项目中，所有的动作都返回了承诺，所有的动作都将被组件通过 mapDispatchToProps 函数调用。使用来自 redux-thunk setup promise 的 thunk 中间件提供了一些功能，我能够编写这样的动作函数。

```
import actionType from '../action-type';
import {api} from './'

export const autoLogin = () => (dispatch, getState) => new Promise((resolve,reject)=>{
    dispatch({
        type: actionType.LOGGED_IN,
        payload: true
    })
    resolve('success');
}).catch(err=>{
    console.log("error here", err)
    throw err
});

export const signup = (params) => (dispatch, getState) => new Promise(async (resolve, reject)=> {
    // do cool stuff then resolve/reject
    try{
        let res = await api.post('/auth/register', params)
        dispatch(autoLogin()).then(()=>{
            resolve({status:'success',message:'You successfully signed up!!!', ...res});
        }).catch(err=>{
            reject({status:'error',message:'Something went wrong!!!', ...res});
        })
    }catch(err){
        console.log(err)
        reject(err);
    }
}).catch(err=>{
    console.log(err)
    throw err
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，注册函数和自动登录都使用承诺来处理它们的操作。从而允许我们推迟基于这些操作的操作，直到它们完成。为了与 API 端点进行交互，我编写了一个名为 API 的类，用于通过 axios 与 API 进行交互。

```
import axios from 'axios'

let headers = {}, running = 0;

class API{

    SIGNUP = ''
    LOGIN =  ''
    CREATE_PROJECT = ''

    constructor(options = {}){
        this.axios = axios.create(options);
        this.headers = {}
    }

    send(type, args) {
        running++;
        const respond = (func, resp) => {
            running--;
            if (!resp) resp = {};
            func(resp.data, resp.headers, resp.status);
        };
        return new Promise(
            function(resolve, reject) {
                this[type]
                    .apply(this, args)
                    .then(resp => {
                        respond(resolve, resp);
                    })
                    .catch(error => {
                        let resp = error.response;
                        respond(reject, resp);
                    });
            }.bind(this.axios)
        );
    }

    get(){
        return this.send('get', arguments)
    }

    post(){
        return this.send('post', arguments)
    }

    put(){
        return this.send('put', arguments)
    }

    patch(){
        return this.send('patch', arguments)
    }

    delete(){
        return this.send('delete', arguments)
    }

    getHeaders(){
        return headers
    }

    getHeader(name) {
        return headers[name];
    }

    headerIs(name, value) {
        return headers[name] == value;
    }
    setHeaders(new_headers) {
        headers = new_headers;
        return this.renew();
    }

    setHeader (key, value) {
        this.axios.defaults.headers.common[key] = value
        return this
    }

    removeHeader(name, norenew) {
        delete headers[name];
        if (!norenew) this.renew();
        return this;
    }
    renew() {
        this.axios = axios.create({
            // baseURL: process.env.API_BASE_URL,
            headers: this.headers
        });
        return this;
    }
}

export default API; 
```

Enter fullscreen mode Exit fullscreen mode

在它的基础上，它实际上是一个简单的类，用于操作标头并确保在所有请求上提供正确的承载令牌，因为 axios 的更改会影响整个应用程序，此类的更改通过索引文件
中定义的一个实例贯穿整个项目

```
const api = new API({
    baseURL : env.API_BASE_URL,
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是在所有操作中导入的实例。

## 祝酒词和通知词。

好的用户界面可以让用户知道已经发生了什么，或者正在发生什么，或者是否发生了错误。有了基于承诺的流，通常很容易捕捉到这些错误，而且由于所有的“then”动作和捕捉短语都是在组件中处理的，我们基本上可以使用基于组件的 toast 处理程序或通知程序。

在这个项目中，我选择了在 npm 上可用的 react-toastify 插件组件。

## 结论

这个星期将完成所有相应的前端实现希望，我真的希望使这个计数(把我的小丑鼻子)。在这个阶段之后，我们将探索 API，我将深入了解 spectre 如何与版本控制系统和服务器相关联地工作。