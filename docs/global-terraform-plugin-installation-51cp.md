# 全局 Terraform 插件安装

> 原文：<https://dev.to/c33s/global-terraform-plugin-installation-51cp>

只需将这个`TF_PLUGIN_CACHE_DIR`添加到你的环境变量中，将来用`terraform init`下载的插件就会到那里:

```
TF_PLUGIN_CACHE_DIR=$HOME/.terraform.d/plugin-cache 
```

Enter fullscreen mode Exit fullscreen mode

链接:

*   封面图片由 geralt/Gerd Altmann[https://pixabay.com/en/arrows-center-inside-middle-1426326/](https://pixabay.com/en/arrows-center-inside-middle-1426326/)
*   [https://github . com/hashi corp/terra form/issues/15801 # issue comment-408645788](https://github.com/hashicorp/terraform/issues/15801#issuecomment-408645788)
*   [https://www . terraform . io/docs/configuration/providers . html # provider-plugin-cache](https://www.terraform.io/docs/configuration/providers.html#provider-plugin-cache)