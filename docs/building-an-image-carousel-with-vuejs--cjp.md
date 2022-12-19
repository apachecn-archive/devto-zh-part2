# 用 Vue.js 构建图像轮播

> 原文：<https://dev.to/tsanak/building-an-image-carousel-with-vuejs--cjp>

在我作为 web 开发人员的几年经验中，我用过十几个 carousel 插件，其中一些真的很好，一些相当笨拙。

最近，我想使用一个小的图像转盘(为我的一个个人项目)来覆盖卡片的上半部分。轮播应该有箭头和缩略图来浏览图像，缩略图应该在轮播的底部。

我很想使用类似 owl-carousel 的东西，继续我的一天，但我也想用 Vue 构建一些新的东西，我已经学习了几个月了。

由于我有充足的时间，我启动了我最喜欢的 IDE，开始了一个新项目。

对于这个项目，我使用了[布尔玛](https://bulma.io/)(一个基于 Flexbox 的 CSS 框架)，当然还有 [Vue.js](https://vuejs.org/) 。如果你是 Vue 新手，我鼓励你阅读[我的第一篇帖子](https://dev.to/tsanak/make-your-life-easier-with-vuejs-4mj5)和令人敬畏的[文档](https://vuejs.org/v2/guide/)。

### [先睹为快](#sneak-peek)

[![alt text](../Images/2e0407be59f04d596af69898065d6dc0.png "Small image carousel with Vue")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wSRV-1NK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lqd5gxqbkwkmoj02dfvi.gif)

### 让我们来谈技术

项目的 HTML 框架

```
<section class="section" id="app">
    <div class="columns">
        <div class="column is-4">
            <div class="card">
                <div class="card-content">
                    <div class="card-carousel">
                        <div class="card-img">
                            <img src="/some-image.jpg" alt="Some image">
                            <div class="actions">
                                <span class="prev">
                                    <i class="fas fa-chevron-left"></i>
                                </span>
                                <span class="next">
                                    <i class="fas fa-chevron-right"></i>
                                </span>
                            </div>
                        </div>
                        <div class="thumbnails">
                            <div class="thumbnail-img">
                                <img src="/some-thumbnail.jpg" alt="Some thumbnail">
                            </div>
                            <div class="thumbnail-img active">
                                <img src="/some-thumbnail.jpg" alt="Some thumbnail">
                            </div>
                            <div class="thumbnail-img">
                                <img src="/some-thumbnail.jpg" alt="Some thumbnail">
                            </div>
                        </div>
                    </div>
                    <p>Card description.</p>
                </div>
            </div>
        </div>
    </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

在一点 CSS 之后，它看起来像这样:
[![alt text](../Images/f6157d6a04f7f70594b708ce6569ae79.png "Carousel skeleton")](https://res.cloudinary.com/practicaldev/image/fetch/s--x00GYTPs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rlrtp2zzunh2hh7sh3xr.png)

既然我们已经解决了样式问题，我们可以把注意力放在功能上了🤖

```
<div class="card-carousel">
    <div class="card-img">
        <img :src="currentImage" alt="">
        <div class="actions">
            <span @click="prevImage" class="prev">
                <i class="fas fa-chevron-left"></i>
            </span>
            <span @click="nextImage" class="next">
                <i class="fas fa-chevron-right"></i>
            </span>
        </div>
    </div>
    <div class="thumbnails">
        <div 
            v-for="(image, index) in  images"
            :key="image.id"
            :class="['thumbnail-image', (activeImage == index) ? 'active' : '']"
            @click="activateImage(index)"
        >
            <img :src="image.thumb">
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

简短说明

```
<!-- 
    Bind the source of the image to a variable,
    so when the variable changes, so does the photo
-->
<img :src="currentImage" alt="">
<!-- 
    Loop through the images array,
    @click="activateImage(index)" on click call the function activateImage 
    :class="['thumbnail-image', (activeImage == index) ? 'active' : '']"
    bind the class of the div to an array. Always show 'thumbnail-image' class 
    and show the class 'active' only for the image that is currently active
-->
<div 
    v-for="(image, index) in  images"
    :key="image.id"
    :class="['thumbnail-image', (activeImage == index) ? 'active' : '']"
    @click="activateImage(index)"
>
    <img :src="image.thumb">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

以及所有需要的 JS 代码:

```
 var app = new Vue({
        el: '#app',
        data() {
            return {
                //Array to hold all carousel images
                images: [
                    {
                        id: '1',
                        big: 'images/p1.jpeg',
                        thumb: 'images/thumbs/p1.jpeg'
                    },
                    {
                        id: '2',
                        big: 'images/p2.jpeg',
                        thumb: 'images/thumbs/p2.jpeg'
                    },
                    {
                        id: '3',
                        big: 'images/p3.jpeg',
                        thumb: 'images/thumbs/p3.jpeg'
                    },
                    {
                        id: '4',
                        big: 'images/p4.jpeg',
                        thumb: 'images/thumbs/p4.jpeg'
                    }
                ],
                //Index of the active image on the images array
                activeImage: 0
            }
        },
        computed: {
            // currentImage gets called whenever activeImage changes
            // and is the reason why we don't have to worry about the 
            // big image getting updated
            currentImage() {
                return this.images[this.activeImage].big;
            }
        },
        methods: {
            // Go forward on the images array 
            // or go at the first image if you can't go forward :/ 
            nextImage() {
                var active = this.activeImage + 1;
                if(active >= this.images.length) {
                    active = 0;
                }
                this.activateImage(active);
            },
            // Go backwards on the images array 
            // or go at the last image
            prevImage() {
                var active = this.activeImage - 1;
                if(active < 0) {
                    active = this.images.length - 1;
                }
                this.activateImage(active);
            },
            activateImage(imageIndex) {
                this.activeImage = imageIndex;
            }
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

这是完成的项目(再次😅)
[![alt text](../Images/2e0407be59f04d596af69898065d6dc0.png "Small image carousel with Vue")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--wSRV-1NK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lqd5gxqbkwkmoj02dfvi.gif)

🎉谢谢你通读这些，我希望你能找到一些有用的东西🎉

你可以在 github 上找到所有的代码

所有照片均取自 Pexels.com
[https://www . pexels . com/photo/bang-blast-庆典-color-287487/](https://www.pexels.com/photo/bang-blast-celebration-color-287487/)
[https://www . pexels . com/photo/person-hands-squaw-fruit-112352/](https://www.pexels.com/photo/person-hands-squash-fruit-112352/)
[https://www . pexels . com/photo/action-blur-car-daylight-246320/](https://www.pexels.com/photo/action-blur-car-daylight-246320/)
[https:/](https://www.pexels.com/photo/auto-automobile-blur-bokeh-242276/)