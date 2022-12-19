# vue-本地无限卷轴

> 原文：<https://dev.to/abiodunjames/vue-native-infinite-scroll-453g>

[![](../Images/81fee2498ac1e5284b66a9ad885ee001.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KoBxDL_o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/samueljames/image/upload/v1540737925/infinite-scrolling.gif) 
本帖旨在向你展示如何在 [Vue-native](https://vue-native.io/) 中实现无限滚动。毫无疑问，无限滚动是对大型数据集进行分页的最佳选择，尤其是在移动应用程序中。

令人兴奋的是，使用 [Vue-native](https://vue-native.io/) 只需几个简单的步骤就可以实现无限滚动

## 我们会用什么

*   [req API](https://reqres.in/)
*   [Vue-native](https://vue-native.io)
*   [轴](https://reqres.in/)

### 安装 Vue-native-cli

如果没有安装 vue-native cli ，请安装它。

```
npm install -g vue-native-cli 
```

### 启动一个 Vue-native 项目

```
vue-native init infinteScroll

npm install 
```

从如下所示的选项中选择`blank`:

[![Blank](../Images/ee61dadb03f0bfe6c5a1e0d7b9349b1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dlYg9V03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/samueljames/image/upload/c_scale%2Ch_786/v1540740190/Screen_Shot_2018-10-28_at_16.09.51.png)

此时，启动一个 [vue-native](https://vue-native.io) 应用程序所需的一切都应该已经创建好了。太棒了。对吗？让我们继续安装更多的依赖项。

导航到项目根目录并运行:

```
npm install -s axios native-base 
```

### 制作一个“用户列表”组件

创建目录`component`并在目录中添加一个文件`UserList.vue`。

```
// component/UserList.vue

<template>
    <nb-container>
        <nb-header>
                <nb-title>Infinite Scrolling</nb-title>
        </nb-header>
        <scroll-view :on-scroll="(event) => {loadMore(event)}" :scroll-event-throttle="400">
            <nb-content>
                <nb-list-item avatar v-for="user in users">
                    <nb-left>
                        <nb-thumbnail small :source="{uri: user.avatar}"/>
                    </nb-left>
                    <nb-body>
                        <nb-text>{{user.first_name}} {{user.last_name }}</nb-text>
                    </nb-body>
                </nb-list-item>
            </nb-content>
        </scroll-view>
        <view :style="{flex: 1, justifyContent: 'center'}" v-if="loading">
            <activity-indicator size="large" color="gray"/>
        </view>
    </nb-container> 
</template> 
<script>
    import axios from "axios"

    export default {
        data: function () {
            return {
                loading: false,
                users: [],
                per_page: 15
            }
        },
        mounted: function () {
            this.getData();
        },

        methods: {
            getData: function () {
                let uri = 'https://reqres.in/api/users';
                this.loading = true;
                axios.get(uri, {
                    params: {
                        per_page: this.per_page
                    }
                }).then((result) => {
                    let response = result.data.data;
                    console.log(response);
                    for (let i in response) {
                        this.users.push(response[i]);
                    }
                    this.loading = false;
                }).catch((error) => {
                    console.log(error)
                })
            },
            loadMore: function (event) {
                let paddingToBottom = 0;
                paddingToBottom += event.nativeEvent.layoutMeasurement.height;
                if (event.nativeEvent.contentOffset.y >= event.nativeEvent.contentSize.height - paddingToBottom) {
                    this.getData()
                }
            }
        }
    }
</script> 
```

### userlist . vue 到底发生了什么？

我们监听每帧触发一次的`scroll event`，然后调用接收`event`作为参数的`loadMore`。基本上，它检测滚动的结束，如果用户已经滚动到底部，它就调用`getData`。

我们通过将`scroll-event-throttle`设置为`400`来控制该事件的触发频率。

当用户向下滚动时，我们也想给他们一种正在加载更多数据的感觉，所以，我们添加了当`loading`为真时可见的`activity-indicator`。

### 裹掉它

如果你看一下根文件夹，你会看到`App.vue`——一个当我们运行`vue-native init infinteScroll`来生成样板文件时创建的文件。

我们用
更新`App.vue`

```
//App.vue

<template>
    <view class="container">
        <nb-container>
            <root>
                <user-list v-if="isReady"></user-list>
            </root>
        </nb-container>
    </view> </template> 
<script>
    import UserList from "./component/UserList"
    import {Root, VueNativeBase} from "native-base";
    import Vue from "vue-native-core";

    Vue.use(VueNativeBase);
    export default {
        data: function () {
            return {
                isReady: false
            }
        },
        created: function () {
            this.loadAppFonts();
        },
        methods: {
            loadAppFonts: async function () {
                try {
                    this.isReady = false;
                    await Expo.Font.loadAsync({
                        Roboto: require("native-base/Fonts/Roboto.ttf"),
                        Roboto_medium: require("native-base/Fonts/Roboto_medium.ttf"),
                        Ionicons: require("@expo/vector-icons/fonts/Ionicons.ttf")
                    });
                    this.isReady = true;
                } catch (error) {
                    console.log("Can't load fonts", error);
                    this.isReady = true;
                }
            },
        },
        components: {
            Root, UserList
        }

    }
</script> <style>
    .container {
        justify-content: center;
        flex: 1;
    }
</style> 
```

上面，我们导入了`UserList`组件，这样我们就可以在`App.vue`(在根实例中)中重用它，并定义了一个方法:`loadAppFonts`异步加载本机所需的自定义字体。

### 预览 app

如果你在 iOS 上并且安装了 iOS 模拟器，你可以通过运行`npm run ios`来预览应用。在 Android 上，运行`npm run android`(必须安装 Android 构建工具)

你也可以在这里找到项目