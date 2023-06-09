# 适用于您的开发环境的 Docker

> 原文：<https://dev.to/littlestudent/docker-for-your-dev-environment-41jp>

几周前，我在想如何让新员工或很少这样做的后端开发人员更容易建立前端项目的现有代码库。为了使这成为一个可靠的过程而不会遇到很多不利因素，我们可以使用`Docker`。

### 对解决方案的要求

*   更改文件时的重新编译也必须如此
*   编译时间，尤其是初始编译时间不应该变得更慢

### Dockerfile

```
FROM node

RUN mkdir /app 
WORKDIR /app

COPY package.json /app/package.json
COPY package-lock.json /app/package-lock.json
RUN npm install

COPY ./config /app/config
COPY ./public /app/public
COPY ./src /app/src
COPY ./tests /app/tests
COPY ./.git /app/.git

RUN npm run build

CMD npm run start

EXPOSE 3000 
```

Enter fullscreen mode Exit fullscreen mode

```
docker build ./ -t web-devserver
docker run -it -p 3000:3000 -v $(pwd)/src:/app/src -v $(pwd)/config:/app/config -v $(pwd)/public:/app/public web-devserver 
```

Enter fullscreen mode Exit fullscreen mode

*   拆分 Dockerfile 文件中的副本条目。这使得增量构建更快，因为只要复制的文件保持不变，Docker 就可以跳过这一步
*   做一次`RUN npm run build`,这样当`npm run start`编译项目时，它只需要做一次增量编译，而不是一次干净的编译。
*   使用选项`-v`将相应的文件夹装入 Docker 容器。这允许在文件更改时进行重建。