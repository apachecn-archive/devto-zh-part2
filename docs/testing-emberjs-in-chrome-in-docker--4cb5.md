# 在 Docker 中测试 Chrome 中的 EmberJS 应用

> 原文：<https://dev.to/michalbryxi/testing-emberjs-in-chrome-in-docker--4cb5>

假设你已经有了一个 *ember-cli* 应用。首先，让我们创建一个`Dockerfile` :

```
# Dockerfile

FROM node:10-alpine

RUN npm install -g ember-cli

RUN \
  echo http://dl-3.alpinelinux.org/alpine/edge/main >> /etc/apk/repositories  && \
  apk add chromium

WORKDIR /myapp

COPY . /myapp/

RUN yarn

CMD ["ember", "test"] 
```

Enter fullscreen mode Exit fullscreen mode

然后你需要修改你的应用程序`testem.js`。注意，我们用的不是**铬**，而是**铬** :

```
module.exports = {
  test_page: 'tests/index.html?hidepassed',
  disable_watching: true,
  launch_in_ci: [
    'Chromium'
  ],
  launch_in_dev: [
    'Chromium'
  ],
  browser_args: {
    Chromium: {
      ci: [
        // --no-sandbox is needed when running Chrome inside a container
        // process.env.CI ? '--no-sandbox' : null,
        '--no-sandbox',
        '--headless',
        '--disable-gpu',
        '--disable-dev-shm-usage',
        '--disable-software-rasterizer',
        '--mute-audio',
        '--remote-debugging-port=0',
        '--window-size=1440,900'
      ].filter(Boolean)
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

构建您的容器:

```
$ docker build -t myapp . 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，您的测试应该能够运行:

```
$ docker run myapp 
Could not start watchman
Visit https://ember-cli.com/user-guide/#watchman for more info.
cleaning up...
Built project successfully. Stored in "/myapp/tmp/class-tests_dist-RpV40XiD.tmp".
ok 1 Chrome 68.0 - [3 ms] - ESLint | app: app.js
ok 2 Chrome 68.0 - [0 ms] - ESLint | app: resolver.js
ok 3 Chrome 68.0 - [0 ms] - ESLint | app: router.js
ok 4 Chrome 68.0 - [0 ms] - ESLint | tests: test-helper.js
ok 5 Chrome 68.0 - [2 ms] - ember-qunit: Ember.onerror validation: Ember.onerror is functioning properly

1..5
# tests 5
# pass  5
# skip  0
# fail  0

# ok 
```

Enter fullscreen mode Exit fullscreen mode