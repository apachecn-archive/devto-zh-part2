# 玩比特桶管道和 Kubernetes

> 原文：<https://dev.to/jurriaanpro/spielerei-with-bitbucket-pipelines-and-kubernetes-16d1>

因此，我最近试用了 Bitbucket 管道，看看它的功能是什么，并看看我是否可以快速(在 1 天内)为 React 应用程序建立一个完整的 CI/CD 管道。下面是结果！

一些先决条件:

*   比特币账户
*   一个 Google Cloud 帐户/项目，因此您可以创建一个容器注册表和 Kubernetes 集群
*   熟悉基本的`gcloud` / `kubectl`命令

# 步骤 1:创建远程存储库

使用您的 Bitbucket 帐户登录并创建一个存储库

[![Create repository](../Images/0f9872a803adab3cf0f584ed6b8b529b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AnhfQyQh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/chxhruvbslna2p9fdy79.png)

单击“创建存储库”后，您将可以使用一个空存储库。

# 第二步:创建 React app

使用 [create-react-app](https://github.com/facebook/create-react-app) ，创建一个新的 react 应用。

我们不打算添加任何功能，所以让它保持原样。

您可以通过运行`npm start`或`yarn start`然后浏览到 [http://localhost:3000](http://localhost:3000) 在浏览器中查看它，来验证它是否已经成功创建。

# 第三步:推送至远程

在新创建的 React 应用程序中，初始化一个 Git 存储库。然后将文件夹中的所有代码推送到远程，这样我们就可以开始使用 Bitbucket 管道了。

```
cd <directory-of-react-app>
git init
git remote add origin https://<username>@bitbucket.org/<username>/<repo-name>.git
git add --all
git commit -m "Initial commit"
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

# 第三步:构建 React app

现在，在您新创建的 Bitbucket 存储库中，单击 Pipelines。

这将为您提供一个可以从模板中进行选择的屏幕。点击 Javascript 并复制给定的模板。

然后在`<directory-of-react-app>`中，用自己喜欢的文本编辑器创建一个`bitbucket-pipelines.yml`并粘贴模板。

将`npm run build`添加到`scripts`部分，以构建 React 应用程序的生产就绪版本，稍后我们将使用该版本将应用程序打包到 Docker 容器中。

```
image: node:6.9.4

pipelines:
  default:
    - step:
        caches:
          - node
        script:
          - npm install
          - npm test
          - npm run build 
```

Enter fullscreen mode Exit fullscreen mode

通过点击 Pipelines 选项卡，提交并推送文件，并在 Bitbucket 上验证是否被 Pipelines 插件拾取。

```
git add bitbucket-pipelines.yml
git commit -m "Added bitbucket-pipelines.yml"
git push 
```

Enter fullscreen mode Exit fullscreen mode

[![Enable Pipelines](../Images/b5c5a14888ea18adcecc372438e80f3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rES6qf3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bu2zgh6m6d8kksxqcpgc.png)

单击 Enable 启动您的第一个构建！

如果一切顺利的话，你最终会得到这样一个屏幕。

[![First build!](../Images/bbc609a9f4785d54b9b2f858c50dd915.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rWKyWnKV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2yniul8jxrtarzms28se.png)

调查所执行步骤的日志，这样您就可以对 UI 有所了解，并知道出现问题时应该去哪里查找。

# 第四步:构建 Docker 容器

我们希望将 React 应用程序部署到 Kubernetes，因此需要将应用程序打包到 Docker 容器中。

在目录的根目录下创建一个`Dockerfile`，内容如下:

```
FROM nginx

COPY build /usr/share/nginx/html 
```

Enter fullscreen mode Exit fullscreen mode

然后在`bitbucket-pipelines.yml`中，通过在文件顶部添加以下内容来启用 Docker:

```
options:
  docker: true 
```

Enter fullscreen mode Exit fullscreen mode

并将下面几行添加到`script`部分来构建容器:

```
- docker build -t example-react-app:$BITBUCKET_COMMIT . 
```

Enter fullscreen mode Exit fullscreen mode

环境变量`$BITBUCKET_COMMIT`自动被提交散列填充。

提交并推送您的更改，然后等待它再次构建。

再说一次，如果一切顺利，您最终应该会看到这样的屏幕。

[![Build #2](../Images/232029858d075514d50ea23cd77140da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o01R7zme--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ardfkwj6bmrn0cbq1ilk.png)

与前一个屏幕的一个不同之处是日志概览中`Build`选项卡旁边的`docker`选项卡。这是 docker 守护进程，随您的构建一起启动，能够运行`docker`命令。

# 第五步:推到 GCR

下一步是将 Docker 容器推送到注册中心。我们可以把它推送到 Docker hub，但是因为我们想学习新的东西，所以我们会把它推送到一个私人的谷歌容器注册中心，T2，GCR。

打开谷歌云控制台，进入容器注册表(在工具下)。如果被询问，启用 API。

为了能够将图像从 Bitbucket 管道推送到注册表，我们将使用一个 [JSON 密钥文件](https://cloud.google.com/container-registry/docs/advanced-authentication#using_a_json_key_file)进行认证。

在 IAM & admin >服务帐户下，创建一个启用了角色`Storage Object Creator`和`Storage Object Viewer`的服务帐户。

创建后，在服务帐户概览中，单击选项列中的菜单，然后单击创建密钥。选择 JSON 作为密钥类型，并将密钥保存到桌面。

我们将把这些凭证作为[安全环境变量](https://confluence.atlassian.com/bitbucket/environment-variables-794502608.html)注入到构建中。

打开 Bitbucket 项目，点击 Settings-> environmental variables，添加一个环境变量(如`GCR_KEY`)，以 JSON key 文件的内容作为值。选中安全复选框，然后单击添加。

[![Environment variables](../Images/a8a572547bcda8627fb947d023c76d20.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_9j33lIO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cr4e5t46wh9hck5piq4e.png)

然后调整`bitbucket-pipelines.yml`，使其看起来像这样:

```
options:
  docker: true

image: node:6.9.4

pipelines:
  default:
    - step:
        name: Build & Test
        caches:
          - node
        script:
          # Set IMAGE_NAME variable used throughout step
          - export IMAGE_NAME=gcr.io/<google-cloud-project-name>/<application-name>:$BITBUCKET_COMMIT
          # Install npm modules
          - npm install
          # Run tests
          - npm test
          # Create production build
          - npm run build
          # Build Docker
          - docker build -t $IMAGE_NAME .
          # Authenticate against Google Cloud Registry using service account JSON key
          - docker login -u _json_key -p "$GCR_KEY" https://gcr.io
          - docker push $IMAGE_NAME 
```

Enter fullscreen mode Exit fullscreen mode

我们在`script`部分添加了几个步骤，并给我们的步骤命名。这些更改是不言自明的，所以让我们提交并推动我们的更改，然后等待构建运行！

构建完成后，您应该会看到这样的屏幕:

[![Push to GCR](../Images/2e83477cac6007948571c2aeb87724b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L46dppBO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/chpt5mw9l5kiqzep0q0u.png)

可以看到，我们已经成功地使用服务帐户登录，并且能够将容器推送到注册中心。接下来，部署！

# 第六步:持续部署

## 步骤 6a:创建 Kubernetes 集群

创建一个 Kubernetes 集群，并配置您的终端，使其能够使用`kubectl`与之通信。

## 步骤 6b:创建初始部署

使用下面的`kubectl run`命令在 Kubernetes 中创建一个占位符部署对象:

```
kubectl run <application-name> --labels="app=<application-name>" --image=gcr.io/<google-cloud-project-name>/<application-name>:latest --replicas=2 --port=80 
```

Enter fullscreen mode Exit fullscreen mode

如果您随后运行`kubectl get po`，您将看到 pod 的状态为`ImagePullBackOff`，因为我们还没有推送标签为`latest`的容器。

一旦我们在 Bitbucket 管道中设置了其余的 CD 部分，这个问题就会得到解决。

## 步骤 6c:设置 Kubernetes 认证和授权

为了对 Kubernetes 集群进行身份验证，我们将使用一个[服务帐户](https://kubernetes.io/docs/admin/authentication/#service-account-tokens)进行身份验证，使用 [RBAC](https://kubernetes.io/docs/admin/authorization/rbac/) 进行授权。

### 步骤 6c1:认证

创建一个服务帐户，从它生成的密码中查看令牌和 CA:

```
$ kubectl create serviceaccount bitbucket
$ kubectl get serviceaccounts bitbucket -o yaml
apiVersion: v1
kind: ServiceAccount
...
secrets:
- name: bitbucket-token-9s6fw
$ kubectl get secrets bitbucket-token-9s6fw -o yaml
apiVersion: v1
data:
  ca.crt: <base64-encoded-ca.crt>
  namespace: ZGVmYXVsdA==
  token: <base64-encoded-token>
kind: Secret
... 
```

Enter fullscreen mode Exit fullscreen mode

复制`ca.crt`后面的值，并在 Bitbucket 中创建一个名为`KUBE_CA`的新的安全环境变量。对`token`后面的值做同样的处理，称之为`KUBE_TOKEN`。

[![Environment variables](../Images/425a305be0513a1678f2aa7d935ed861.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A5cQMUn_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ry1n40otinqqt9rau6lk.png)

### 步骤 6c2:授权

首先，通过运行以下命令让自己成为集群管理员( [credits](https://medium.com/@lestrrat/configuring-rbac-for-your-kubernetes-service-accounts-c348b64eb242) ):

```
ACCOUNT=$(gcloud info --format='value(config.account)')
echo $ACCOUNT
kubectl create clusterrolebinding owner-cluster-admin-binding 
        --clusterrole cluster-admin
        --user $ACCOUNT 
```

Enter fullscreen mode Exit fullscreen mode

然后创建以下角色和角色绑定对象，以允许 serviceaccount 部署我们应用程序的新版本。

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: deploy
rules:
- apiGroups: ["extensions", "apps"]
  resources: ["deployments"]
  verbs: ["get", "update", "patch"]

--------

kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: deploy-role-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: bitbucket
  namespace: default
roleRef:
  kind: Role
  name: deploy
  apiGroup: rbac.authorization.k8s.io 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们可以继续配置管道来部署新的映像。

## 步骤 6d:配置 Bitbucket 流水线

向您的`bitbucket-pipelines.yml`添加一个包含以下内容的新步骤:

```
- step:
    name: Deploy to Test
    deployment: test
    script:
      # Set IMAGE_NAME variable used throughout step
      - export IMAGE_NAME=gcr.io/<google-cloud-project-name>/<app-name>:$BITBUCKET_COMMIT
      # Download kubectl
      - curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
      - chmod +x ./kubectl
      - mv ./kubectl /usr/local/bin/kubectl
      # Configure kubectl
      - echo $KUBE_TOKEN | base64 --decode > ./kube_token
      - echo $KUBE_CA | base64 --decode > ./kube_ca
      - kubectl config set-cluster <cluster-name> --server=<address-of-cluster> --certificate-authority="$(pwd)/kube_ca"
      - kubectl config set-credentials bitbucket --token="$(cat ./kube_token)"
      - kubectl config set-context development --cluster=<cluster-name> --user=bitbucket
      - kubectl config use-context development
      - kubectl set image deployment/<app-name> <app-name>=$IMAGE_NAME 
```

Enter fullscreen mode Exit fullscreen mode

这会将`kubectl`下载到构建过程中使用的容器中，使用前面步骤中提取的信息对其进行配置，然后使用`kubectl set image`部署新版本的应用程序。使用`kubectl cluster-info`可以找到集群的地址。

提交并推动这些更改，然后等待 Bitbucket 管道发挥它的魔力！

[![Yay!](../Images/18d154994e56de3688d8309856ffa32d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z8KzpYuf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wx1vrz0nx2g9o5jysb1q.png)

(如果您足够快，可以运行以下命令来跟踪卷展栏的状态)

```
$ kubectl rollout status deployment <app-name>
Waiting for rollout to finish: 1 old replicas are pending termination...
Waiting for rollout to finish: 1 old replicas are pending termination...
Waiting for rollout to finish: 1 of 2 updated replicas are available...
Waiting for rollout to finish: 1 of 2 updated replicas are available...
deployment "<app-name>" successfully rolled out 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用诸如`kubectl get po`和`kubectl describe po <pod-name>`这样的命令来进一步调查属于该部署的 pod，以检查它们是否正在运行正确的容器。

# 就是这样！

我们已经使用 Bitbucket 管道和 Kubernetes 为 React 应用程序设置了一个完整(但简单)的 CI/CD 链！我花了一天多一点的时间来弄清楚一些事情，因为我已经使设置的某些部分变得更加困难，唯一的目的是学习新的东西(在基本 HTTP Auth 上使用服务帐户/RBAC 等)。)!

感谢你的阅读，祝你快乐！