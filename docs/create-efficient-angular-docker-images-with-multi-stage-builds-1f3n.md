# 通过多阶段构建创建高效的角度 Docker 图像

> 原文：<https://dev.to/avatsaev/create-efficient-angular-docker-images-with-multi-stage-builds-1f3n>

在这篇文章中，我们将看到如何使用 Docker 的多阶段构建以一种高效的方式将 Angular 应用程序 Docker 化。

*在写这篇文章的时候，我正在使用 Angular v7*

# 先决条件

*   NodeJS +8
*   角度坐标(`npm i -g @angular/cli@latest`)
*   Docker +17.05
*   对 Docker 和 Angular CLI 命令有基本了解

# 计划

要对使用 Angular CLI 构建的基本 Angular 应用程序进行文档化，我们需要执行以下操作:

*   npm 安装依赖项(包括开发依赖项)
*   带- prod 标志的 ng 构建
*   将工件从`dist`文件夹移动到一个可公开访问的文件夹(通过 nginx 服务器)
*   设置一个 nginx 配置文件，并启动 http 服务器

[![Ultimate Courses](img/12cdf6285fac1e316bd47871a099ef74.png)T2】](https://ultimatecourses.com/courses/angular/ref/azero79/ "Ultimate Courses")

**我们将分两个阶段进行**:

*   构建阶段:将依赖于节点 alpine Docker 映像
*   设置阶段:将取决于 NGINX alpine Docker 图像，并使用来自构建阶段的工件，以及来自我们项目的 NGINX 配置。

## 初始化一个空角项目

`$ ng new myapp`

## 添加默认 nginx 配置

在 Angular 项目的根目录下，创建 nginx 文件夹，并创建一个名为 default.conf 的文件，其中包含以下内容(。/nginx/default.conf):

```
server {

  listen 80;

  sendfile on;

  default_type application/octet-stream;

  gzip on;
  gzip_http_version 1.1;
  gzip_disable      "MSIE [1-6]\.";
  gzip_min_length   1100;
  gzip_vary         on;
  gzip_proxied      expired no-cache no-store private auth;
  gzip_types        text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
  gzip_comp_level   9;

  root /usr/share/nginx/html;

  location / {
    try_files $uri $uri/ /index.html =404;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 Docker 文件

```
 ### STAGE 1: Build ###

# We label our stage as ‘builder’
FROM  node:10-alpine  as  builder

COPY package.json package-lock.json ./

## Storing node modules on a separate layer will prevent unnecessary npm installs at each build

RUN npm ci && mkdir /ng-app && mv ./node_modules ./ng-app

WORKDIR /ng-app

COPY . .

## Build the angular app in production mode and store the artifacts in dist folder

RUN npm run ng build -- --prod --output-path=dist

### STAGE 2: Setup ###

FROM nginx:1.14.1-alpine

## Copy our default nginx config
COPY nginx/default.conf /etc/nginx/conf.d/

## Remove default nginx website
RUN rm -rf /usr/share/nginx/html/*

## From ‘builder’ stage copy over the artifacts in dist folder to default nginx public folder
COPY --from=builder /ng-app/dist /usr/share/nginx/html

CMD ["nginx", "-g", "daemon off;"] 
```

Enter fullscreen mode Exit fullscreen mode

## 建立形象

`$ docker build -t myapp .`

## 运行容器

$ dock run-p 8080:80 myapp

完成后，您的 dockerized 应用程序将可在 [http://localhost:8080](http://localhost:8080) 访问

而镜像的大小只有~15.8MB，一旦推送到 Docker 库就更少了。

[![](img/73407ee6cdefaa6bb186448903f21991.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CWgI9NS6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0f6v2ku7edmm2yguz8o9.png)

你可以在这个 GitHub 库中看到一个完整的例子:[https://github.com/avatsaev/angular-contacts-app-example](https://github.com/avatsaev/angular-contacts-app-example)