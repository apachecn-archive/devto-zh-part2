# 在构建期间强制使用 kubernetes 版本

> 原文：<https://dev.to/chuck_ha/forcing-a-kubernetes-version-during-build-5dfi>

```
Date: November, 19th, 2018
Kubernetes: https://github.com/kubernetes/kubernetes/commit/8848740f6d0f84c2c4c5165736e12425551a6207
The current release is just before v1.13.0 so the newest tag is v1.14.0-alpha. 
```

Enter fullscreen mode Exit fullscreen mode

当一个 [Kubernetes](https://github.com/kubernetes/kubernetes/) 二进制文件用 [bazel](https://bazel.build/) 构建时，它会为你设置一个二进制文件的默认版本。也就是说，当您运行`kubectl version`时，版本会自动填充。

然而，有时你想要一个定制的版本，可能是为了测试的目的。例如，如果你正在测试 kubeadm 的升级功能，它有助于强制一个版本避免一些讨厌的行为。

Bazel 通过一个名为`--workspace_status_command`的标志定义的脚本来计算这个版本。该参数在[文件](https://github.com/kubernetes/kubernetes/blob/8848740f6d0f84c2c4c5165736e12425551a6207/build/root/.bazelrc)中指定。

`print-workspace-status.sh`调用一个 bash 函数并以 bazel 使用的特定格式打印一些版本信息。

如果你想定制它，你有两个选择。

#### 选项 1 您可以定义一个定制的`--workspace_status_command`脚本，用您想要的任何版本生成这些版本

将该文件创建为`workspace-status.sh`，并使其可执行`chmod +x workspace-status.sh`。

```
#!/usr/bin/env bash

cat <<EOF gitCommit $(git rev-parse "HEAD^{commit}") gitTreeState clean
gitVersion v2.0.0
gitMajor 2
gitMinor 0
buildDate $(date \
  ${SOURCE_DATE_EPOCH:+"--date=@${SOURCE_DATE_EPOCH}"} \
 -u +'%Y-%m-%dT%H:%M:%SZ')  EOF 
```

Enter fullscreen mode Exit fullscreen mode

现在运行`bazel build --workspace_status_command=./workspace-status.sh //cmd/kubeadm`，你可以看到当你运行`kubeadm version`并得到这个输出:

```
kubeadm version
kubeadm version: &version.Info{Major:"2", Minor:"0", GitVersion:"v2.0.0", GitCommit:"679d4397cfdb386ebd3ae4bcb9972273b3f75ca3", GitTreeState:"clean", BuildDate:"2018-11-19T20:43:30Z", GoVersion:"go1.11.2", Compiler:"gc", Platform:"darwin/amd64"} 
```

Enter fullscreen mode Exit fullscreen mode

#### 选项 2

您可以定义一个环境变量`KUBE_GIT_VERSION_FILE`，该变量定义一个已经指定了版本的文件。

创建一个名为`version.txt`的文件，并将以下内容放入其中:

```
KUBE_GIT_COMMIT=abcd
KUBE_GIT_TREE_STATE="clean"
KUBE_GIT_VERSION="v2.0.3"
KUBE_GIT_MAJOR=2
KUBE_GIT_MINOR=0 
```

Enter fullscreen mode Exit fullscreen mode

现在用`export KUBE_GIT_VERSION_FILE=version.txt`获取该文件作为环境变量并运行`bazel build //cmd/kubeadm`。

然后，当您运行`kubeadm version`时，您将看到:

```
$ kubeadm version
kubeadm version: &version.Info{Major:"2", Minor:"0", GitVersion:"v2.0.3", GitCommit:"abcd", GitTreeState:"clean", BuildDate:"2018-11-19T20:58:25Z", GoVersion:"go1.11.2", Compiler:"gc", Platform:"darwin/amd64"} 
```

Enter fullscreen mode Exit fullscreen mode