# 如何擦干你的眼泪？gitlab-ci.yml

> 原文：<https://dev.to/michalbryxi/how-to-dry-your-gitlab-ciyml-16pc>

玩着玩着，我发现了一些我想分享的好东西。先说个例子:

```
.frontend_template: &frontend_template
  stage: deploy
  script:
  - npm install netlify-cli -g
  - ember deploy $ENVIRONMENT --verbose
  - netlify deploy -e $ENVIRONMENT -p ./dist -s $NETLIFY_SITE_ID -t $NETLIFY_KEY

.frontend_variables: &frontend_variables
  API_SERVER: "http://${ENVIRONMENT}-api.example.com"
  NETLIFY_SITE_ID: "${ENVIRONMENT}-example-com"

development_frontend:
  <<: *frontend_template
  variables:
    ENVIRONMENT: "development"
    <<: *frontend_variables
  only:
  - master

production_frontend:
  <<: *frontend_template
  variables:
    ENVIRONMENT: "production"
    <<: *frontend_variables
  only:
  - production 
```

Enter fullscreen mode Exit fullscreen mode

事情还挺多的，我们试着一点一点分解一下:

*   每个以点号开头的[作业定义](https://docs.gitlab.com/ee/ci/yaml/README.html#jobs)都会被忽略。那么`.frontend_template`和`.frontend_variables`是不是*而不是*定义了活动的、可运行的作业。这个功能叫做[隐藏键](https://docs.gitlab.com/ee/ci/yaml/#hidden-keys-jobs)。
*   允许引用自身的其他部分，防止复制&粘贴。这个节目叫做[主播](https://docs.gitlab.com/ee/ci/yaml/#anchors)。简单来说:
    *   `&frontend_template`是*复制*
    *   `<<: *frontend_template`是*粘贴*
*   分开`frontend_template`和`frontend_variables`可能看起来很奇怪，但是 YAML 不允许在使用锚点时进行复杂的合并，所以`variables`键不能简单地放入`frontend_template`，因为它会被`development_frontend` / `production_frontend`级的同名键覆盖。
*   在定义`ENVIRONMENT`之后导入`frontend_variables`很重要，因为这样我们就可以在`API_SERVER` / `NETLIFY_SITE_ID`中使用那个变量。

额外收获:如果你想知道你的 YAML 扩展后是什么样子，你可以简单地把你的源代码转换成不支持锚点的东西。比如 JSON。[用上面的例子试试](http://yaml-online-parser.appspot.com/)。