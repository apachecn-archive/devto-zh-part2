# 用 Docker 构建 Tomcat 和 MySQL

> 原文：<https://dev.to/mfurmaniuk/building-out-tomcat-and-mysql-with-docker-3fn2>

(原谅格式，仍然习惯于降价)

我们有一个运行在 Tomcat 上的测试队列和存档服务器，数据存储在 MySQL 中。这目前在我们实验室的虚拟机上运行，但作为将其迁移到 AWS 的过程的一部分，我将这些的手动设置迁移到 Docker 映像。这给了我们一些优势:

1.  一个长时间、多天的 wiki 页面安装被浓缩成一个 docker 映像，在 5 分钟内完成
2.  无论何时我们需要部署，我们都可以获得一个可重复使用的环境，而不用担心配置文件的底层更改
3.  能够构建环境的本地开发版本，并在几分钟内启动和使用，而不是转移到开发虚拟机并为我们很少使用的环境运行一些安装脚本。这个系统很少更新。

因为我喜欢自动化，所以我添加了一些本地 shell 脚本来检查正在运行的 Docker 映像和容器，并为之前的构建添加了清理功能。在测试中，我一遍又一遍地构建这些映像，发现我有一个不需要的 5 GB 的先前构建映像。脚本还处理停止容器，这样我就不会有冲突，以防我忘记检查容器是否在运行，有时我在周末后不会检查我的笔记本电脑，看看我可能还剩下什么在运行。我知道，那是个坏习惯，但是这样我就不用担心了。

对于我的本地开发环境，我知道我将在哪个 IP 上启动我的容器，所以我在我的主机文件中添加了一个条目，以便能够在容器启动时使用它的命名实例。比起担心尝试连接到一个 IP 地址，我更喜欢这样。

作为将它添加到 Jenkins 中的一部分，我向 shell 脚本添加了参数，以构建我需要的环境，本地开发环境需要 MySQL，生产环境有一个运行的 RDS DB，因此我所需要的就是 Tomcat。这样，所有的配置都在 docker 文件中设置，我只需构建我想要的环境。

> **#！/bin/bash**
> 
> # 为可在本地环境中使用的本地 Tomcat 构建脚本
> 
> BUILD='LOCAL '
> 
> 显示帮助()
> 
> {
> 
> cat << EOF
> 用法:
> $0 -b local 用于本地迅雷和 MySQL Docker(默认)
> $0 -b prod 用于远程迅雷 Docker 映像连接到 AWS RDS
> 
> EOF
> }
> 
> 而 getopts " HB:" opt；do
> case $ opt in
> h)
> show _ help
> exit 0
> ；；
> b)
> BUILD = $ OPTARG
> ；；esac
> 完成
> 
> # WAR 文件和 web 服务器文件
> 
> *附和*“我们应该总是想要做一个干净的然后一个新的构建”
> T3】wget-O www/scripts/jquery . js code.jquery.com/jquery-1.11.1.js
> *CP*www/index . html . templ www/index . html
> *。/grad Lew*clean build
> **if[-e**build/libs/my . war**然后**
> ** ** *echo* “我的. war 构建正确”
> **else**
> * * * **echo*“我的. war 没有构建，某种问题？”
> *退出* 1
> **fi**
> 
> # 停止现有图像，如果它们正在运行
> 
> LOCAL =*`docker ps -q --filter=ancestor=local`*
> PROD =*`docker ps -q --filter=ancestor=prod`*
> MYSQL =*`docker ps -q --filter=ancestor=mysql`*
> **if[**！**-z**" $ LOCAL "**]**；**然后**
> ** ** *回显*“停止运行本地实例”
> *docker* 停止本地
> **fi**
> **if**！**-z**" $ PROD "**；**然后**
> ** ** *echo* “停止运行 Prod 实例”
> *docker*stop Prod
> **fi**
> **if**！**-z**" $ MYSQL "**；**然后**
> ** ** *echo* “停止 MySQL 实例”
> *docker* 停止 mysql
> **fi******
> 
>  ****# 让我们干干净净地去掉所有那些旧的危险的图像
> 
> 系统修剪-f
> 
> # 创建一个 Docker 图像
> 
> **如果[**"**$**{ BUILD } "**= =**' LOCAL '**]那么**
> ** ** *echo* -e "运行一个$ BUILD BUILD "
> # BUILD all for LOCAL 但只需要 Tomcat for Prod
> *docker*BUILD-t MySQL-f deployment/docker/LOCAL/MySQL/docker file。
> *docker*build-t local-f 部署/docker/local/Tomcat/docker file。
> **else**
> * * * **echo*-e“运行一个$ BUILD BUILD”
> # Prod 版本应该是 Prod 名称，不需要数据库
> *docker*BUILD-t Prod-f deployment/docker/Prod/Tomcat/docker file。
> **fi**
> 
> # 清理我们只需要的文件为 war 文件
> 
> *RM*www/index . html
> T3】RMwww/scripts/jquery . js****

 ****对于 Tomcat，我需要对上下文和服务器文件进行一些配置，这将因环境而异，但我特别需要的是 Docker 中的一种方法，在没有人工干预的情况下构建自签名证书。经过一番搜索和反复试验，我终于能够想出构建它所需要的命令了。

> **#安装有用的工具运行 apt-get 更新& & **
> ** apt-get -y 升级&&* *
> * * apt-get Install-y wget vim curl procps tar libssl-dev-fix-missing-no-Install-recommenders**
> 
> **#配置 Tomcat 容器**
> **复制 build/libs/my . war/usr/local/Tomcat/web apps/**
> **复制部署/docker/local/resources/local-server . XML/usr/local/Tomcat/conf/server . XML**
> **复制部署/docker/local/resources/local-context . XML/usr/local/Tomcat/conf/context . XML**
> **复制部署/docker/**
> 
> **# MySQL 连接器**
> 
> **WORKDIR/usr/local/Tomcat/lib/**
> **运行 curl-L-o mysql-connector-java-5.1.46.tar.gz[https://dev . MySQL . com/get/Downloads/Connector-J/MySQL-Connector-Java-5 . 1 . 46 . tar . gz](https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz)**
> **#在 MySQL-Connector-Java-5 . 1 . 46/MySQL-Connector-Java-5 . 1 . 46 . jar**T10
> 
> **# Configure Tomcat User and GROUP ENV RUN _ User Tomcat**
> **ENV RUN _ GROUP Tomcat**
> **RUN GROUP add-R $ { RUN _ GROUP }&&useradd-g $ { RUN _ GROUP }-d $ { CATALINA _ HOME }-s/bin/bash $ { RUN _ User }&&* *
> * * chown-R $ { RUN _ User }:$ { RUN _ User } $ CATALINA _ HOME RUN chown-R $ { R $ }$ { RUN _ USER }/data/RUN chown-R $ { RUN _ USER }。＄{ RUN _ USER }/usr/local/resource-manager/**
> 
> **# Setup SSL for Tomcat**
> **WORKDIR/usr/local/Tomcat/conf/RUN $ JAVA _ HOME/bin/keytool-genkey-alias Tomcat-keyalg RSA-store pass change it-key pass change it-no prompt-dname " CN = local . test . server，O=MYCOMP，L=BOSTON，S=Massachusetts，C = US "-keystore "/usr/local/Tomcat/conf/。keystore"**
> **运行 OpenSSL req-new key RSA:2048-x509-subj "/C = US/ST = Massachusetts/L = BOSTON/O = my comp/CN = local . test . server "-keyut cakey . PEM-out cacert . PEM-passout pass:my pass**
> **运行 OpenSSL PKCS12-export-in cacert . PEM-inkey cakey . PEM-out identity . p12-name " local "-in pass:my pass**
> 
> **工作目录/usr/本地/tomcat/**
> 
> **用户 tomcat**

对于生产版本，我需要一个实际的证书，但是由于环境还没有完全准备好，我还不需要导入它。

有一个 shell 脚本可以让本地开发环境运行起来，使用 Docker-Compose 我可以让我需要的环境运行起来。同样，shell 脚本为本地和生产环境提供了一个适当的设置路径。

> **BUILD = ' LOCAL '**
> **show _ help()**
> **{**
> **cat<<EOF**
> * *用法:*
> ** $0 -b LOCAL 为本地迅雷和 MySQL Docker(默认)**
> ** $0 -b PROD 为远程迅雷 Docker 镜像连接 AWS RDS * *
> **EOF**
> **}**
> 
> **while getopts " HB:" opt**
> **do**
> * * case " $ opt " in * *
> * * h)* *
> * * show _ help * *
> * * exit 0 * *
> * *；；* *
> * * b)* *
> * * BUILD = $ OPTARG * *
> * *；；**
> ** esac**
> **完成**
> 
> **#停止现有图片，如果正在运行**
> **LOCAL =`docker ps -q --filter=ancestor=local`**
> **PROD =`docker ps -q --filter=ancestor=prod`**
> **MYSQL =`docker ps -q --filter=ancestor=mysql`**
> 
> **如果[！-z " $ LOCAL "]；然后**
> ** echo“停止运行本地实例”**
> ** docker 停止本地**
> **fi**
> **if [！-z " $ PROD "]；然后**
> **回显“停止运行 Prod 实例”**
> ** docker 停止 prod**
> **fi**
> 
> **如果[！-z " $ MYSQL "]；然后**
> ** echo“停止 MySQL 实例”**
> ** docker 停止 mysql**
> **fi**
> 
> **#检查本地或生产构建，然后使用正确的目录**
> **if[" $ { BUILD } " = = ' Local ']然后**
> ** echo -e "启动$构建环境" **
> ** docker-compose -f 部署/docker/Local/Tomcat . YAML-verbose up-d * *
> **else**
> * * echo-e "启动$构建环境" **
> ** docker-compose -f 部署/docker/Prod/tomt

我为环境设置的 YAML 文件让容器按照我需要的设置启动并运行。

> **服务:**
> * * Tomcat:*
> * * container _ name:Tomcat * *
> * * image:local:最新**
> **环境:*
> * * CATALINA _ OPTS:"-server-xms 1024m-xmx 2048m-XX:PermSize = 1024m-XX:MaxPermSize = 1536m-Dhost _ default _ domain = tlocal . test . server-DJ ava . net . preferip v4 stack = true-agent lib:jdwp = transport = dt _ XX

这样，我就可以启动并运行我的 Tomcat 和 MySQL 容器，我可以将一些本地测试指向测试服务器 URL，并在本地运行我的测试和归档它们的运行。这给了我测试变更的灵活性，因为我可以在本地构建我的 Docker 环境并进行测试，而不用担心签入存储库和签出另一个可能正在使用或可能不在使用的环境。****