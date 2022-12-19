# 使用 CircleCI 将 Ruby 应用程序持续部署到 Google Cloud Kubernetes 引擎

> 原文：<https://dev.to/evilmartians/deploying-ruby-apps-to-google-cloud-kubernetes-engine-continuously-with-circleci-2765>

我已经很久没有机会尝试 Kubernetes 了，但几周前我终于有了一个机会:我一直在开发一个简单的 Ruby 应用程序(Slack bot)，并决定将其部署到谷歌云 [Kubernetes 引擎](https://cloud.google.com/kubernetes-engine/)。

还需要建立持续部署流程。为此，我选择了 [CircleCI](https://circleci.com) 服务。

**注意:**这篇文章只是一个行动手册，包含了我为启动和运行应用程序所做的所有操作。

软件版本:

*   Kubernetes **1.9.4-gke.1**
*   谷歌云 SDK **195.0.0**

## 准备 GCloud

让我们假设你已经有一个谷歌云账户。

转到[http://console.cloud.google.com/](http://console.cloud.google.com/)并创建一个启用了 Kubernetes API 的新项目(`my-project`)。

下一步–安装 [`gcloud`](https://cloud.google.com/sdk/) CLI:

```
# for MacOS/Homebrew users it's pretty simple
brew install caskroom/cask/google-cloud-sdk 
```

Enter fullscreen mode Exit fullscreen mode

然后你必须验证你自己:

```
gcloud auth login 
```

Enter fullscreen mode Exit fullscreen mode

(*可选的*)配置默认项目(避免每个命令都指定):

```
gcloud config set project my-project 
```

Enter fullscreen mode Exit fullscreen mode

创建 K8S 集群:

```
# "g1-small" is a minimal instance type (as of the time of writing) that allows having only one node
gcloud container clusters create my-cluster --machine-type g1-small --num-nodes 1 
```

Enter fullscreen mode Exit fullscreen mode

获取集群的凭证:

```
gcloud container clusters get-credentials my-cluster 
```

Enter fullscreen mode Exit fullscreen mode

获取集群列表以验证一切正常:

```
gcloud container clusters list 
```

Enter fullscreen mode Exit fullscreen mode

输出如下所示:

```
NAME         LOCATION    MASTER_VERSION  MASTER_IP      MACHINE_TYPE  NODE_VERSION  NUM_NODES  STATUS
my-app-web  us-east1-b  1.9.4-gke.1     35.227.92.102  g1-small      1.9.4-gke.1   1          RUNNING 
```

Enter fullscreen mode Exit fullscreen mode

## 准备申请

正如我已经说过的，我想要部署的应用程序是一个简单的纯 Ruby 应用程序(即，没有数据库、缓存、持久存储)。关于如何部署 Rails 应用程序，请参见本文末尾的链接。

我们需要建立一个 Docker 映像，并推送到 [Google 容器注册表](https://cloud.google.com/tools/container-registry/)。

下面是我的`Dockerfile` :

```
FROM ruby:2.5.0

RUN apt-get update && apt-get install -y build-essential git

RUN mkdir -p /app
WORKDIR /app

ENV LANG C.UTF-8

COPY Gemfile Gemfile.lock ./
RUN gem install bundler && bundle install --jobs 20 --retry 5

COPY . .

# We want to include the current git revision information
# to a build to track releases
ARG BUILD_SHA=unknown

ENV BUILD_SHA ${BUILD_SHA}

EXPOSE 3000

ENTRYPOINT ["bundle", "exec"]

CMD ["puma", "-p", "3000"] 
```

Enter fullscreen mode Exit fullscreen mode

让我们来建造它:

```
docker build -t my-app -f Dockerfile.prod . 
```

Enter fullscreen mode Exit fullscreen mode

**注:**我用`Dockerfile.prod`文件生产，`Dockerfile`开发。

然后使用特定的 GCloud 格式(包含片段和项目名称)对其进行标记:

```
docker tag my-app us.gcr.io/my-project/my-app:v1 
```

Enter fullscreen mode Exit fullscreen mode

并推:

```
gcloud docker -- push us.gcr.io/my-project/my-app:v1 
```

Enter fullscreen mode Exit fullscreen mode

好的。我们快到了。现在，我们必须以某种方式告诉我们的 K8S 集群获取此映像并运行。

为此，我们将使用 K8S 部署:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  labels:
    name: web
spec:
  replicas: 2
  selector:
    matchLabels:
      name: web
  template:
    metadata:
      labels:
        name: web
    spec:
      containers:
        - name: web
          image: gcr.io/my-project/my-app:latest
          ports:
            - containerPort: 3000
          livenessProbe:
            httpGet:
              path: /_health
              port: 3000
            initialDelaySeconds: 10
            timeoutSeconds: 1
          readinessProbe:
            httpGet:
              path: /_health
              port: 3000
            initialDelaySeconds: 10
            timeoutSeconds: 1 
```

Enter fullscreen mode Exit fullscreen mode

K8S 文档是阅读部署的好地方；这里不用回忆了。

我只想关注一下`livenessProbe`和`readinessProbe`部分:它们告诉 K8S 如何监控应用程序状态以提供滚动更新功能，并确保所需数量的副本启动并运行。注意，您的应用程序应该为这些检查提供端点(在我的例子中是`/_health`)。

现在我们需要再安装一个工具—`kubectl`—来操作我们的集群:

```
gcloud components install kubectl 
```

Enter fullscreen mode Exit fullscreen mode

是时候部署我们的应用程序了！让我们创建我们的部署:

```
kubectl create -f kube/web-deployment.yml 
```

Enter fullscreen mode Exit fullscreen mode

要获得关于我们部署的信息，您可以查看[*pod*](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/):
的列表

```
kubectl get pods 
```

Enter fullscreen mode Exit fullscreen mode

关于 pod 的更多信息:

```
kubectl describe pod web-<pod-id> 
```

Enter fullscreen mode Exit fullscreen mode

### 手动更新应用

您的应用程序已经启动并正在运行。如何推送新发布？

如果您想更新您的部署配置，那么只需运行下面的命令:

```
kubectl apply -f kube/web-deployment.yml 
```

Enter fullscreen mode Exit fullscreen mode

为了更新代码库，您应该告诉您的集群使用新的映像:

```
docker tag my-app us.gcr.io/my-project/my-app:v1.1
gcloud docker -- push us.gcr.io/my-project/my-app:v1.1
kubectl set image deployment web web=gcr.io/my-project/my-app:v1.1 
```

Enter fullscreen mode Exit fullscreen mode

K8S 将负责创建新的 pod 并替换旧的 pod。

运行三个命令，手动提供新版本，不是最方便的部署方式吗？

让别人来做所有的脏活累活吧。

## CircleCI 集成

CircleCI 2.0 是一个非常灵活的自动化工具。

我们想让我们的部署像制作`git push`(或者将 PR 合并到`master`分支)一样简单。

下面你可以找到一个例子`.circle/config.yml`和解释每个步骤的注释:

```
version: 2
workflows:
  version: 2
  build_and_deploy:
    jobs:
      - build
      - deploy:
          # Run the "deploy" job only if the "build" job was successful
          requires:
            - build
          # Run deploy job only on master branch
          filters:
            branches:
              only:
                - master
jobs:
  # This job is responsible for running tests and linters
  build:
    docker:
      - image: circleci/ruby:2.5.0
        environment:
          RACK_ENV: test
    steps:
      - checkout
      - run:
          name: Install gems
          command: bundle install
      - run:
          name: Run Rubocop and RSpec
          # Our default rake task contains :spec and :rubocop tasks
          command: bundle exec rake
      # Cache all the project files to re-use in the deploy job
      # (instead of pulling the repo again)
      - persist_to_workspace:
          root: .
          paths: .
  deploy:
    docker:
      # official image which includes `gcloud` and `kubectl` tools
      - image: google/cloud-sdk
        # project information
        environment:
          GOOGLE_PROJECT_ID: my-project
          GOOGLE_COMPUTE_ZONE: us-east1-b
          GOOGLE_CLUSTER_NAME: my-app
    steps:
      # Attach previously cached workspace
      - attach_workspace:
          at: .
      # SERVICE_KEY provides access to your GCloud project.
      # Read more here https://circleci.com/docs/2.0/google-auth/
      - run: echo $GCLOUD_SERVICE_KEY > ${HOME}/gcloud-service-key.json
      # Authenticate gcloud
      - run: gcloud auth activate-service-account --key-file=${HOME}/gcloud-service-key.json
      # Configure gcloud (the same steps as you do on your local machine)
      - run: gcloud --quiet config set project ${GOOGLE_PROJECT_ID}
      - run: gcloud --quiet config set compute/zone ${GOOGLE_COMPUTE_ZONE}
      - run: gcloud --quiet container clusters get-credentials ${GOOGLE_CLUSTER_NAME}
      # Enable remote Docker (https://circleci.com/docs/2.0/building-docker-images/)
      - setup_remote_docker
      # Build Docker image with the current git revision SHA
      - run: docker build -t brooder -f Dockerfile.prod --build-arg BUILD_SHA=${CIRCLE_SHA1} .
      # Use the same SHA as our image version
      - run: docker tag brooder gcr.io/my-project/my-app:${CIRCLE_SHA1}
      # Using remote Docker is a little bit tricky but this "spell" works
      - run: gcloud docker --docker-host=$DOCKER_HOST -- --tlsverify --tlscacert $DOCKER_CERT_PATH/ca.pem --tlscert $DOCKER_CERT_PATH/cert.pem --tlskey $DOCKER_CERT_PATH/key.pem push gcr.io/my-project/my-app:${CIRCLE_SHA1}
      # and finally, "deploy" the new image
      - run: kubectl set image deployment web web=gcr.io/my-project/my-app:${CIRCLE_SHA1} --record 
```

Enter fullscreen mode Exit fullscreen mode

## 保守秘密

让我们在这里再讨论一个话题——管理应用程序秘密。

我们使用 [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 来存储敏感信息(比如第三方服务 API 令牌)。

首先，你必须创建秘密定义文件:

```
# kube/app-secrets.yml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  # The value should be BASE64 encoded
  github_token: b2t0b2NhdA==
  slack_token: Y3V0bWVzb21lc2xhY2s= 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记将值编码成 base64。例如:

```
echo -n "myvalue" | base64 
```

Enter fullscreen mode Exit fullscreen mode

向集群推送秘密:

```
kubectl create -f kube/app-secrets.yml
#=> secret "mysecret" created 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**推至 K8S 后立即移除`app-secrets.yml`(或至少添加到`.gitignore`)。

现在你可以通过 env 变量将秘密传递给你的应用程序:

```
# web-deployment.yml
apiVersion: apps/v1
kind: Deployment
# ...
spec:
  # ...
  templaee:
    # ...
    spec:
      containers:
        - name: web
          image: gcr.io/my-project/my-app:latest
          # ....
          env:
            - name: MY_GITHUB_TOKEN
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: github_token
            - name: MY_SLACK_TOKEN
              valueFrom:
                secretKeyRef:
                  name: mysecret
                  key: slack_token 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

*   [立方概念](https://kubernetes.io/docs/concepts/)
*   [Kubernetes 和部署到谷歌容器引擎](http://www.thagomizer.com/blog/2015/07/01/kubernetes-and-deploying-to-google-container-engine.html)
*   [在 Kubernetes 上部署导轨](https://engineering.adwerx.com/rails-on-kubernetes-8cd4940eacbe)
*   [Rails 5.2 凭证和 Kubernetes](http://kirshatrov.com/2018/03/24/rails-credentials-kubernetes/)

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！