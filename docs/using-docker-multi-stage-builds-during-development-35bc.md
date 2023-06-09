# 在开发过程中使用 Docker 多阶段构建

> 原文：<https://dev.to/brpaz/using-docker-multi-stage-builds-during-development-35bc>

多阶段构建是 Docker 17.05 引入的一个功能，它允许您从同一个 Docker 文件创建多个中间映像。

对于多阶段构建，可以在 docker 文件中使用多个 FROM 语句。每个 FROM 指令可以使用不同的基础，并且每个指令都开始构建的新阶段。您可以有选择地将工件从一个阶段复制到另一个阶段，在最终图像中留下所有不想要的东西。你可以在这里阅读更多关于多阶段构建的信息。

这非常有用，例如，在您的最终映像中不包括应用程序构建依赖项，允许您拥有一个更小的映像。

在生产映像中有一个二进制文件很好，但是开发呢？您可能需要您的构建依赖项，并且建议生产和开发使用相同的 docker 文件。

有一段时间，并不清楚如何做到这一点，但它只是一个标志。

诀窍是使用 build 命令的“target”标志，它允许您指定希望在哪个阶段停止构建。

例如:

考虑下面的 Dockerfile，它负责构建一个基于 [Jekyll](https://jekyllrb.com/) 的静态站点。

```
FROM ruby:2.5.1-alpine3.7  AS build-env
RUN apk update && apk add --no-cache nodejs build-base
RUN apk add yarn --no-cache --repository http://dl-3.alpinelinux.org/alpine/v3.8/community/ --allow-untrusted
RUN mkdir -p /app
WORKDIR /app
COPY Gemfile Gemfile.lock ./
RUN bundle install -j 4
COPY . ./
COPY package.json yarn.lock ./
RUN yarn install
RUN make _site

VOLUME /app

FROM nginx:1.13.0-alpine
WORKDIR /usr/share/nginx/html
COPY --from=build-env /app/_site ./

EXPOSE 80 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这个 Dockerfile 文件有两个“FROM”指令。它们中的每一个都代表了多阶段构建中的一个阶段。

在第一阶段，我安装了所有必要的工具来构建 Jekyll 应用程序，如 ruby 和 bundler，以及这个 specefic 站点所需的前端依赖项 yarn。

这个阶段的最终结果是一个名为“_site”的文件夹，里面有一堆 HTML、Javascript 和 CSS。在生产中，我只想提供这个静态内容，我不需要在最终图像中构建站点所需的所有 ruby 依赖项，所以我基于 nginx-alpine image 创建了一个新的阶段，我只是将构建阶段生成的站点内容复制到其中。

如果我现在构建图像，最终的图像将只有生成的网站。

这对于生产使用来说很棒，但是在开发期间，我不希望每次做更改时都必须构建站点，并且希望有像“热重新加载”和“动态”资产编译这样的好东西。

这就是“目标”标志发挥作用的地方。此标志允许您指定希望构建在哪个阶段停止。所以如果你指定:

```
docker build . --target=build-env 
```

Enter fullscreen mode Exit fullscreen mode

你会有一个与那个阶段的内容完全相同的图像。与 docker-compose 它甚至更简单。

```
version: '3.4'
services:
  web:
    build: 
      context: .
      target: build-env
    volumes:
      - .:/app
    ports:
      - '8082:80'
    command: 'env PORT=4001 HOST=0.0.0.0 yarn run dev' 
```

Enter fullscreen mode Exit fullscreen mode

*注意*:目标需要合成版本> 3.4。

因此，当在我的开发环境中运行

`docker-compose up`

时，我将得到一个映像，其中包含所有开发依赖项和挂载为卷的源代码，我可以使用 live reload 立即看到我的更改。

就这样。

理解多阶段构建是如何工作的，打开你的思维去思考许多可能的用例，例如在生产构建之前有一个安装测试依赖项的阶段来运行单元测试。