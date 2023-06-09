# 带有 vue 组件的简单注释部分

> 原文：<https://dev.to/tsanak/simple-comment-section-with-vue-components-1cdm>

最近我开始将我工作的前端部分从 jquery 迁移到 Vue.js，感觉很棒！我一直喜欢在前端工作，但是和 Vue 一起工作感觉很好。

因为我们试图缓慢地迁移，所以我们目前使用 vue 的 UMD 版本，我们只是用一个脚本标签导入它。这允许我们利用框架提供的大部分特性。

我不得不做的最后一件事是建立一个小的评论区，它将在一些帖子下使用。

### [先睹为快](#sneak-peek)

[![alt text](img/b810009b084916e5a40fe703457dc59f.png "Small comment section with Vue")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--T7xIyGd7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x87l1eqvltc6l40soqr9.gif)

当使用组件时，我喜欢为**应用**创建一个单独的文件，并为每个新的**组件**创建一个新文件。这是个人偏好，其他人使用不同的文件结构。

### 显示代码🤖

```
<div class="comments-outside">
    <div class="comments-header">
        <div class="comments-stats">
            <span><i class="fa fa-thumbs-up"></i> [[ likes ]]</span>
            <span><i class="fa fa-comment"></i> [[ comments.length ]]</span>
        </div>
        <div class="project-owner">
            <div class="avatar">
                <img :src="creator.avatar" alt="">
            </div>
            <div class="username">
                <a href="#">@[[ creator.user ]]</a>
            </div>
        </div>
    </div>
    <comments 
        :comments_wrapper_classes="['custom-scrollbar', 'comments-wrapper']"
        :comments="comments"
        :current_user="current_user"
        @submit-comment="submitComment"
    ></comments>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

应用程序的代码。

```
new Vue({
    el: '#app',
    delimiters: ['[[', ']]'],
    data: function() {
        return {
            likes: 12,
            //Info about the owner of the post
            creator: {
                avatar: 'http://via.placeholder.com/100x100/36846e',
                user: 'owner'
            },
            //Some info about the current user
            current_user: {
                avatar: 'http://via.placeholder.com/100x100/a74848',
                user: 'exampler'
            },
            //Comments that are under the post
            comments: [
                {
                    id: uuidv4(),
                    user: 'example',
                    avatar: 'http://via.placeholder.com/100x100/a74848',
                    text: 'lorem ipsum dolor lorem ipsum dolor lorem ipsum dolor',
                },
            ]
        }
    },
    methods: {
        submitComment: function(reply) {
            this.comments.push({
                id: uuidv4(),
                user: this.current_user.user,
                avatar: this.current_user.avatar,
                text: reply
            });
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将被保存为一个名为 *comments-app.js* 的单独文件，并将包含在 html 中。

所以现在我们有了应用程序的框架和一些数据来开始，但我们仍然需要制作*评论*组件。

预览:
[![alt text](img/e88ddb6886cffdcd80fad3fbdab1b506.png "comments component")](https://res.cloudinary.com/practicaldev/image/fetch/s--h3-fN6RO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vpu2n5yknorxgan70rj.png) 

```
Vue.component('comments', {
    delimiters: ['[[', ']]'],
    template: `
        <div class="comments">
            <div :class="comments_wrapper_classes">
                <single-comment 
                    v-for="comment in comments"
                    :comment="comment"
                    :key="comment.id"
                ></single-comment>
            </div>
            <hr>
            <div class="reply">
                <div class="avatar">
                    <img :src="current_user.avatar" alt="">
                </div>
                <input 
                    type="text" 
                    v-model.trim="reply" 
                    class="reply--text" 
                    placeholder="Leave a comment..."
                    maxlength="250"
                    required
                    @keyup.enter="submitComment"
                />
                <button 
                    class="reply--button" 
                    @click.prevent="submitComment">
                    <i class="fa fa-paper-plane"></i> Send
                </button>
            </div>
        </div>
    `,
    data: function() {
        return {
            reply: ''
        }
    },
    methods: {
        //Tell the parent component(main app) that we have a new comment
        submitComment: function() {
            if(this.reply != '') {
                this.$emit('submit-comment', this.reply);
                this.reply = '';
            }
        }
    },
    //What the component expects as parameters
    props: ['comments', 'current_user', 'comments_wrapper_classes']
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将被放入一个名为 *comments.js* 的新文件中，我们将在应用程序中包含一个脚本标签。
如你所见，我们有一个名为*的新组件，我们需要构建单注释*(我保证这是最后一个😅).
预览:
[![alt text](img/076619a627692deb15262df990dae49c.png "Single comment component")](https://res.cloudinary.com/practicaldev/image/fetch/s--fhH27oLh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xjubaib7c3s7bpq78ulf.png) 

```
Vue.component('single-comment', {
    delimiters: ['[[', ']]'],
    template: `
        <div class="comment">
            <div class="avatar">
                <img :src="comment.avatar" alt="">
            </div>
            <div class="text">
                <a class="username" href="#">
                    @[[ comment.user ]]
                </a> 
                <span>[[ comment.text ]]</span>
            </div>
        </div>
    `,
    props: ['comment']
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将被放入一个名为 *single-comment.js* 的新文件中，我们将在应用程序中包含一个脚本标签。

### 结论

就我个人而言，每当我必须创作任何东西时，我都会把它分解成几个部分，并问自己一些问题。这可以用在其他地方吗？
2。照原样用在别的地方有意义吗？
如果答案是肯定的，我就创建组件。

🎉谢谢你通读这些，我希望你能找到一些有用的东西🎉

让我知道，如果你想看完整的代码，与 css 和我将创建一个 github 回购。