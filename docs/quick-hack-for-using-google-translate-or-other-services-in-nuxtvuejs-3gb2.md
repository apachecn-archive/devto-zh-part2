# 在 Nuxt/Vue.js 中使用谷歌翻译(或其他服务)的快速破解

> 原文：<https://dev.to/coolgoose/quick-hack-for-using-google-translate-or-other-services-in-nuxtvuejs-3gb2>

嘿伙计们，这里有一个使用 setInterval 让谷歌翻译播放更好的快速方法。

```
export default {
    mounted: function()
    {
        this.$nextTick(() => {
            this.googleTranslateInit();
        });

    },

    methods: {

        googleTranslateInit: function() {

            let checkIfGoogleLoaded = setInterval(() => {

                if (google.translate.TranslateElement != null) {
                    clearInterval(checkIfGoogleLoaded);

                    this.googleTranslateElement('google_translate_element');
                }

            }, 100);

        },

        googleTranslateElement: function(id) {
            new google.translate.TranslateElement({pageLanguage: 'en', layout: google.translate.TranslateElement.InlineLayout.SIMPLE}, id);
        }

    },
}; 
```

Enter fullscreen mode Exit fullscreen mode

为 google translate 提供一个好的回调并不总是容易的，尤其是当你嵌套在一个组件中的时候。我只使用 NUXT 的[外部资源](https://nuxtjs.org/faq/)设置来直接加载库，并忽略了为此创建一个插件(尽管我认为这仍然有效，以[创建一个脚本并使用一个 onLoad 函数](https://developer.mozilla.org/en-US/docs/Web/API/HTMLScriptElement)