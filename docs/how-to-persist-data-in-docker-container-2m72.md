# 如何在 docker 容器中持久化数据

> 原文：<https://dev.to/jibinliu/how-to-persist-data-in-docker-container-2m72>

## TL；速度三角形定位法(dead reckoning)

集装箱应该很轻。添加不必要的数据会增加创建和运行的负担。Docker 提供了几种将存储从主机挂载到容器的方法。卷是最常用的一种。它可以用来保存应用程序数据，也可以在多个容器之间共享数据。(本地卷不能在 docker 服务之间共享。您将需要共享存储。)

## 背景

我前阵子听说过 docker 和 container，但是，我是使用它们的新手。直到最近，我才开始探索，因为它有助于构建 web 服务并轻松部署在多个操作系统上。(它们是非常棒的工具！)

对于其中一个 web 服务，它的工作是创建/更新/激活另一个虚拟环境，并使用该环境运行任务。不同的请求有时需要不同的虚拟环境。每个虚拟环境的`requirements.txt`文件会不时地同步，然后调用`pip install`来更新虚拟环境。`pip install`能抓紧时间，需要被叫的次数越少越好。这意味着 web 服务需要持久化虚拟环境，以便当服务重新启动时，它不必重复创建/更新环境作业。

这里它提出了一个问题，每次为 web 服务构建一个新的映像时，显然它没有将虚拟环境存储在旧的容器中。这使得服务“非常冷启动”。为了解决这个问题，我首先想到的是将旧容器的更改提交给新的映像。然而，这极大地增加了图像和容器的大小。

在对 docker 文档进行了几个小时的研究后，我意识到，到目前为止，我一直认为容器是“完全独立的”，而当它与主机一起工作时，它具有更大的能力。

## 解决办法

Docker 提供了三种将数据挂载到容器的方法:卷、绑定挂载和 tmpfs 存储[1]。

*   **卷**是主机文件系统的一部分，但是由 docker 在特定路径上管理，并且不应该被其他应用程序修改
*   **绑定装载**可以在主机上的任何位置，但可以被其他应用程序修改
*   **tmpf**位于主机的内存空间中，永远不会写入文件系统。

一般来说，卷是解决容器中大多数数据持久性问题的首选解决方案。卷既可以通过`docker volume create`命令创建，也可以在启动容器时创建。

## 作为我的解决方案的例子

docker 文档在这里是【2】。

#### 1.创建卷

首先，让我们创建一个名为`virtualenv`的卷，作为存储虚拟环境的路径。

```
➤ docker volume create virtualenv 
```

我们可以通过以下命令来检查卷

```
➤ docker volume inspect virtualenv
[
    {
        "CreatedAt": "2018-09-15T05:29:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/virtualenv/_data",
        "Name": "virtualenv",
        "Options": {},
        "Scope": "local"
    }
] 
```

#### 2.创建容器

示例应用程序的结构如下所示:

*   `Dockerfile`
*   `main.py`:入口点
*   `create_env.sh`(用于创建另一个虚拟环境)

`main.py`做的是检查虚拟环境“my_env”是否存在。如果没有，它将创建它。我们将把上面创建的卷作为`~/.virtualenv`文件夹挂载到容器中。

我使用下面的 Dockerfile 文件来创建最简单的 python 映像:

```
FROM python:3.7
WORKDIR /app
ADD . /app
RUN pip install virtualenv
CMD ["python", "./main.py"] 
```

`main.py`看起来是这样的:

```
import os
import subprocess

def main():
    if os.path.exists('/root/.virtualenv/my_env'):
        print('my_env already exists')
    else:
        subprocess.run(['bash', 'create_env.sh'])
        print('my_env created')

if __name__ == '__main__':
    main() 
```

还有那一行字`create_env.sh`

```
cd ~/.virtualenv/ && virtualenv my_env 
```

#### 3.启动已装载卷的容器

我们首先构建 python 映像:

```
➤ docker build -t docker-data-persistence . 
```

然后，为了挂载卷，我们使用`--mount`参数:

```
➤ docker run \
  --mount source=virtualenv,target=/root/.virtualenv \
  docker-data-persistence

Using base prefix '/usr/local'
New python executable in /root/.virtualenv/my_env/bin/python
Installing setuptools, pip, wheel...done.
my_env created 
```

正如我们在上面看到的，当我们第一次运行容器时，它将创建虚拟环境“my_env ”,因为它还不存在于卷中。如果我们第二次运行它，它会说“my_env”已经存在。

```
➤ docker run \
  --mount source=virtualenv,target=/root/.virtualenv \
  docker-data-persistence

my_env already exists 
```

#### 4.检查音量

我们可以查看卷中的文件(以一种粗略的方式[3])来验证内容:

```
➤ docker run -it \
  --mount source=virtualenv,target=/root/.virtualenv \
  docker-data-persistence \
  find /root/.virtualenv/my_env/bin

/root/.virtualenv/my_env/bin
/root/.virtualenv/my_env/bin/python3
/root/.virtualenv/my_env/bin/activate.csh
/root/.virtualenv/my_env/bin/easy_install-3.7
/root/.virtualenv/my_env/bin/python
/root/.virtualenv/my_env/bin/python-config
/root/.virtualenv/my_env/bin/easy_install
/root/.virtualenv/my_env/bin/python3.7
/root/.virtualenv/my_env/bin/activate
/root/.virtualenv/my_env/bin/pip
/root/.virtualenv/my_env/bin/activate.fish
/root/.virtualenv/my_env/bin/pip3
/root/.virtualenv/my_env/bin/wheel
/root/.virtualenv/my_env/bin/activate_this.py
/root/.virtualenv/my_env/bin/pip3.7 
```

#### 5.删除该卷

要删除卷，我们可以使用`docker volume rm <volume-name>`。但是，当有容器在使用卷时，即使容器已经退出，也不能删除卷。

```
➤ docker volume rm virtualenv
Error response from daemon: remove virtualenv: volume is in use - [dc4425b806a67a9002d68703cdd9854feba44e43d591278b4eb2869f43c0da6d] 
```

## 参考

*   [1][https://docs.docker.com/v17.09/engine/admin/volumes/](https://docs.docker.com/v17.09/engine/admin/volumes/)
*   [2][https://docs . docker . com/v 17.09/engine/admin/volumes/volumes/](https://docs.docker.com/v17.09/engine/admin/volumes/volumes/)
*   [3][https://stack overflow . com/questions/34803466/how-to-list-the-content-of-a-named-volume-in-docker-1-9](https://stackoverflow.com/questions/34803466/how-to-list-the-content-of-a-named-volume-in-docker-1-9)