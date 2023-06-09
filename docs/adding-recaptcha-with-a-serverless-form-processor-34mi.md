# 用无服务器表单处理器添加 reCaptcha

> 原文：<https://dev.to/raymondcamden/adding-recaptcha-with-a-serverless-form-processor-34mi>

几天前，我给 ColdFusion 网站添加了谷歌的 reCaptcha 支持。这非常简单(一些前端工作，一些后端工作)，所以我想我应该快速演示一下如何使用无服务器处理器将它添加到表单中，在我的例子中，是 [Webtask](https://webtask.io) 。首先，在添加验证码之前，让我们快速演示一下这种处理器的外观。

# 公元前(验证码之前)

首先，这是表格。

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        
        <meta name="description" content="">
        <meta name="viewport" content="width=device-width">
        <style>
        [v-cloak] {display: none}
        </style>
    </head>
    <body>

        <form id="infoForm" v-cloak>
            <p>
            <label for="name">Name:</label>
            <input type="text" v-model="name" id="name" required>
            </p>

            <p>
            <label for="email">Email:</label>
            <input type="email" v-model="email" id="email" required>
            </p>

            <p>
            <label for="url">URL:</label>
            <input type="url" v-model="url" id="url">
            </p>

            <p>
                <input type="submit" value="Send Info" @click.prevent="submitForm">
            </p>

            <div v-if="errors.length">
                <p>
                    <b>Please correct these errors:</b>
                    <ul>
                        <li v-for="error in errors">{{error}}</li>
                    </ul>
                </p>
            </div>
            <div v-if="formGood">
                <p>
                    Thanks for filling out the form. We care a lot.
                </p>
            </div>
        </form>

        <script src="https://unpkg.com/vue"></script>
        <script>
        const taskEndpoint = 'https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.run.webtask.io/form_resp1';

        new Vue({
            el:'#infoForm',
            data() {
                return {
                    name:null,
                    email:null,
                    url:null,
                    errors:[],
                    formGood:false
                }
            },
            methods:{
                submitForm() {
                    this.errors = [];
                    this.formGood = false;
                    fetch(taskEndpoint, {
                        body:JSON.stringify({ 
                            name:this.name,
                            email:this.email,
                            url:this.url
                        }),
                        headers: {
                            'content-type':'application/json'
                        }, 
                        method:'POST'
                    })
                    .then(res => res.json())
                    .then(res => {
                        console.log(res);
                        if(res.status) {
                            this.formGood = true;
                        } else {
                            this.errors = res.errors;
                        }
                    });
                }
            }
        });
        </script>

    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我有三个表单字段，并使用 Vue.js 通过 Ajax 处理帖子。我假设所有这些都很容易理解，但是和往常一样，如果您有任何问题，请添加评论。端点是一个 webtask 函数。就是这里:

```
'use strict';

module.exports = function(context, cb) {
    //first, gather the form fields
    console.log(context.body)
    let form = context.body;
    let errors = checkForm(context.body);

    if(errors.length) {
        cb(null, {status: false, errors:errors});
    } else {
        // we'd also email the results here, or store them, or something
        cb(null, {status: true});
    }
}

/* simple validation routine, returns an array of errors */
function checkForm(f) {
    let errors = [];
    if(!f.name || f.name.trim() === '') errors.push("Name required.");
    if(!f.email || f.email.trim() === '') errors.push("Email required.");
    // could add email validation here
    return errors;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个 webtask 中，我简单地获取表单数据(它在`context.body`中，你可以在[文档](https://webtask.io/docs/context)中读到更多关于上下文对象的信息)并将其传递给一个名为 checkForm 的函数。我的表单有三个字段，但是我只关心其中的两个。如果验证失败(通过返回数组中的任何内容)，我将返回 false 状态和错误。否则我返回 true，正如评论所说，我可能会用电子邮件发送表单或以某种方式存储它。

# 空调(~~空调~~验证码后)

使用 Google 的 reCaptcha 包括三个主要步骤:

*   首先，你得到一把钥匙。谷歌现在让这变得简单多了。
*   第二，你添加[前端代码](https://developers.google.com/recaptcha/docs/display)。你有多种选择。
*   最后，在服务器端验证 reCaptcha。

要得到你的钥匙，从这里开始:[http://www.google.com/recaptcha/admin](http://www.google.com/recaptcha/admin)。注意，你实际上得到*两把*钥匙。

[![Screen shot of keys](img/42ae99ebd6f2aa9807af8f3d370f86d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ywIjDV-_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/04/rec1.jpg)

第一个密钥用于前端。第二个密钥在服务器端用于验证。

添加验证码非常简单。放入一个脚本标签，然后添加一个 div:

```
<div class="g-recaptcha" data-sitekey="my site key is blah"></div> 
```

Enter fullscreen mode Exit fullscreen mode

就其本身而言，这将创建一个隐藏的表单字段，当用户检查验证码时，它将填写一个密钥。如果您使用的是 ColdFusion 等“常规”旧服务器，甚至是 Node，那么您将使用获取表单值的典型方式来获取值。然而，在我们的例子中，我们使用客户端代码发送到一个无服务器的 web 钩子，所以我们需要手动获取密钥。下面是更新后的表单(为了缩小尺寸，去掉了一点):

```
<form id="infoForm" v-cloak>
    <p>
    <label for="name">Name:</label>
    <input type="text" v-model="name" id="name" required>
    </p>

    <p>
    <label for="email">Email:</label>
    <input type="email" v-model="email" id="email" required>
    </p>

    <p>
    <label for="url">URL:</label>
    <input type="url" v-model="url" id="url">
    </p>

    <div class="g-recaptcha" data-sitekey="6Ld5WlEUAAAAAJmHfUirSkYnsFk85br615KDYktz"></div>

    <p>
        <input type="submit" value="Send Info" @click.prevent="submitForm" :disabled="disabled">
    </p>

    <div v-if="errors.length">
        <p>
            <b>Please correct these errors:</b>
            <ul>
                <li v-for="error in errors">{{error}}</li>
            </ul>
        </p>
    </div>
    <div v-if="formGood">
        <p>
            Thanks for filling out the form. We care a lot.
        </p>
    </div>
</form>

<script src='https://www.google.com/recaptcha/api.js?onload=onload'></script>
<script src="https://unpkg.com/vue"></script>
<script>
const taskEndpoint = 'https://wt-c2bde7d7dfc8623f121b0eb5a7102930-0.run.webtask.io/form_resp2';

let app = new Vue({
    el:'#infoForm',
    data() {
        return {
            name:null,
            email:null,
            url:null,
            errors:[],
            formGood:false,
            disabled:true
        }
    },
    methods:{
        enable() {
            this.disabled = false;
        },
        submitForm() {
            this.errors = [];
            this.formGood = false;
            fetch(taskEndpoint, {
                body:JSON.stringify({ 
                    name:this.name,
                    email:this.email,
                    url:this.url,
                    recaptcha:grecaptcha.getResponse()
                }),
                headers: {
                    'content-type':'application/json'
                }, 
                method:'POST'
            })
            .then(res => res.json())
            .then(res => {
                console.log(res);
                if(res.status) {
                    this.formGood = true;
                } else {
                    this.errors = res.errors;
                }
            });
        }
    }
});

function onload() {
    app.enable();
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

好吧，有几件事。首先，当我添加脚本标签时，请注意`onload`位:

```
<script src='https://www.google.com/recaptcha/api.js?onload=onload'></script> 
```

Enter fullscreen mode Exit fullscreen mode

这让我可以监听验证码的加载事件。我需要这个，因为我不想让用户在验证码加载之前提交表单。我向我的 Vue 实例添加了一个新变量，该变量在事件触发之前禁用提交按钮。基本上`onload`只是链接到切换值的`app.enable()`。

下一个变化在我的帖子里:

```
body:JSON.stringify({ 
    name:this.name,
    email:this.email,
    url:this.url,
    recaptcha:grecaptcha.getResponse()
}), 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，我使用了一个全局对象`grecaptcha`来从 UI 中获取值。这要么是空的(恶意用户忽略了它)，要么是一个长字符串。它看起来是这样的:

[![Screen shot of form with captcha](img/402a66cd7cbb185b4d371ab6aec873de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w-vG3lMt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/04/rec2.png)

现在我们来看看更新后的 webtask:

```
'use strict';

const request = require('request');

module.exports = function(context, cb) {
    //first, gather the form fields
    let form = context.body;

    checkForm(context.body, context.secrets.recaptcha)
    .then(result => {
        console.log('result was '+JSON.stringify(result.errors));       

        if(result.errors.length) {
            cb(null, {status: false, errors:result.errors});
        } else {
            // we'd also email the results here, or store them, or something
            cb(null, {status: true});
        }

    });

}

/* simple validation routine, returns an array of errors */
function checkForm(f, recaptchaKey) {
    return new Promise((resolve, reject) => {

        let errors = [];
        if(!f.name || f.name.trim() === '') errors.push("Name required.");
        if(!f.email || f.email.trim() === '') errors.push("Email required.");
        // could add email validation here

        request.post(' https://www.google.com/recaptcha/api/siteverify', {
            form:{
                secret:recaptchaKey,
                response:f.recaptcha
            }
        }, (err, resp, body) => {
            if(!JSON.parse(body).success) {
                errors.push('You did not fill out the recaptcha or resubmitted the form.');
            }
            resolve({errors:errors});

        });

    });
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个主要变化是 checkForm 现在是异步的，并返回一个承诺。我这样做是因为我知道我将进行一个 HTTP 调用来验证这个密钥。我现在传递那个键和表单，像这样:

```
checkForm(context.body, context.secrets.recaptcha) 
```

Enter fullscreen mode Exit fullscreen mode

什么是`context.secrets.recaptcha`？Webtasks 允许[秘密](https://webtask.io/docs/editor/secrets)，这对 API 键非常有用。在我的例子中，我简单地通过 CLI 设置密钥:`wt create form_resp2.js --secret recaptcha=mykeywashere`。您也可以在在线编辑器中设置密钥。

在`checkForm`中，你可以看到我对谷歌的验证端点做了一个简单的发布。如果出现任何问题，我会返回一个一般性错误(我可以让它更精确)，然后最终我们会解决这个错误数组。

你可以在这里自己测试一下:[https://cfjedimaster . github . io/server less-Examples/recaptcha/test 2 . html](https://cfjedimaster.github.io/Serverless-Examples/recaptcha/test2.html)

这两个版本的完整源代码可以在这里找到:[https://github . com/cfjedimaster/server less-Examples/tree/master/recaptcha](https://github.com/cfjedimaster/Serverless-Examples/tree/master/recaptcha)

如果你对此有任何疑问，就在下面给我留言吧！