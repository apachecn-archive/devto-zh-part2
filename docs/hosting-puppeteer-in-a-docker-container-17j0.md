# 在 Docker 容器中托管木偶师

> 原文：<https://dev.to/paul_kinlan/hosting-puppeteer-in-a-docker-container-17j0>

这是埃里克·比德尔曼给我的一个剧本(或者说是帮我创作的——我还记得)。

我需要一种简单的方法来托管一个定制的 web 应用程序，该应用程序可以通过 Puppeteer API 连接到 Headless Chrome 的一个实例。这篇文章主要是为将来做一个参考。

docker 容器相对简单:

1.  使用节点:8-slim
2.  安装所有需要的依赖项，包括 Chrome。
3.  初始化环境
4.  从当前目录复制我的应用程序(docker 文件和应用程序在同一个文件夹中)
5.  设置用户和权限
6.  暴露端口并启动应用程序。

```
FROM node:8-slim
LABEL name "puppeteraas"

# See https://crbug.com/795759
RUN apt-get update && apt-get install -yq libgconf-2-4

# Install latest chrome dev package and fonts to support major 
# charsets (Chinese, Japanese, Arabic, Hebrew, Thai and a few others)
# Note: this installs the necessary libs to make the bundled version 
# of Chromium that Puppeteer
# installs, work.
RUN apt-get update && apt-get install -y wget --no-install-recommends \
    && wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' \
    && apt-get update \
    && apt-get install -y google-chrome-unstable fonts-ipafont-gothic fonts-wqy-zenhei fonts-thai-tlwg fonts-kacst ttf-freefont \
      --no-install-recommends \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge --auto-remove -y curl \
    && rm -rf /src/*.deb

# It's a good idea to use dumb-init to help prevent zombie chrome processes.
ADD https://github.com/Yelp/dumb-init/releases/download/v1.2.0/dumb-init_1.2.0_amd64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init

# Uncomment to skip the chromium download when installing puppeteer. 
# If you do, you'll need to launch puppeteer with:
# browser.launch({executablePath: 'google-chrome-unstable'})
# ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD true

# Copy the app
COPY . /app/
#COPY local.conf /etc/fonts/local.conf
WORKDIR app
RUN npm i

# Add user so we don't need --no-sandbox.
RUN groupadd -r pptruser && useradd -r -g pptruser -G audio,video pptruser \
    && mkdir -p /home/pptruser/Downloads \
    && chown -R pptruser:pptruser /home/pptruser \
    && chown -R pptruser:pptruser ./node_modules

# Run everything after as non-privileged user.
USER pptruser

EXPOSE 8084
ENTRYPOINT ["dumb-init", "--"]
CMD ["npm", "run", "start"] 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，我将大部分逻辑抽象到一个名为`kinlan:puppets`的新 docker 容器脚本中，该脚本允许您定制如何部署应用程序。

```
FROM kinlan/puppets:latest

# Copy the app
COPY . /app/
#COPY local.conf /etc/fonts/local.conf
WORKDIR app
RUN npm i

# Add user so we don't need --no-sandbox.
RUN groupadd -r pptruser && useradd -r -g pptruser -G audio,video pptruser \
    && mkdir -p /home/pptruser/Downloads \
    && chown -R pptruser:pptruser /home/pptruser \
    && chown -R pptruser:pptruser ./node_modules

# Run everything after as non-privileged user.
USER pptruser

EXPOSE 8084
ENTRYPOINT ["dumb-init", "--"]
CMD ["npm", "run", "start"] 
```

Enter fullscreen mode Exit fullscreen mode

我将很快发布一个额外的更新，关于我在哪里使用它。