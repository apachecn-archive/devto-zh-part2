# 浏览器中的 vue.js 模块，便宜的方式

> 原文：<https://dev.to/magnusmanske/vuejs-modules-in-the-browser-the-cheap-way-4dbp>

我真的很喜欢 vue.js，更喜欢它 React*et al .*
vue 的一个优势是可以直接在浏览器中使用，而不需要在服务器上进行预处理，用 webpack 或者类似的。但是如何在一个纯粹基于浏览器的设置中分离你的 vue 组件呢？

*   你可以把你的 JavaScript 放到`.js`文件中，但是如何分隔/包含`<template>` s 呢？
*   如何将 JavaScript、模板和(潜在的)CSS 放在一起？

理想情况下，我希望将 JS、模板代码、CSS 和一个组件的潜在依赖项(例如非 vue JS 库)放在一个文件中。然而:

*   如果您的文件包含非 JavaScript 代码，则`<script>`不起作用
*   `<link>`对我没用
*   RequireJS 也不起作用(尽管没有详细说明)
*   后起之秀 ES6 include/require 似乎还不够普及

我找到了一个适合我的低技术解决方案，可能对你们中的一些人有用，所以开始吧:

*   我把所有的 vue 组件放在一个目录中，每个文件一个，例如`component-one.html`
*   每个文件由`<template>`、`<style>`、`<script>`等组成。标签；本质上只是普通的 HTML
*   要在新应用中使用组件，我只需添加以下内容:

```
vue_components.loadComponents (
    ['component-one','component-two','...'] , 
    function(){
        // Initialise router and mount app
        router = new VueRouter({routes}) ;
        app = new Vue ( { router } ) .$mount('#app') ;
    } 
) ;

```

`vue_components`对象在一个单独的 JS 文件中定义(我的版本需要 JQuery，对于`load`方法):

```
var vue_components = {
    template_container_base_id : 'vue_component_templates' ,
    components_base_url : 'https://my.favourite.server/resources/vue/' ,
    loadComponents : function ( components , callback ) {
        var me = this ;
        var cnt = 0 ;
        $.each ( components , function ( dummy , component ) {
            cnt++ ;
            me.loadComponent ( component , function(){
                if ( --cnt == 0 ) callback() ;
            } ) ;
        } ) ;
    } ,
    loadComponent ( component , callback ) {
        var me = this ;
        var id = me.template_container_base_id + '-' + component ;
        if ( $('#'+id).length > 0 ) return callback() ;
        $('body').append($("<div id='"+id+"' style='display:none'>"));
        var url = me.components_base_url+component+'.html' ;
        $('#'+id).load(url,function(){ callback() }) ;
    }
}

```

这将把每个组件添加到它们自己的`<div>`的末尾`<body>`，模板、css、代码等等。只有当所有组件都加载后，路由器和应用程序才会初始化。AFAICT，这适用于所有使用 JQuery 的浏览器。我刚开始使用这个系统，毫无疑问会改进它，但我欢迎任何建设性的反馈！