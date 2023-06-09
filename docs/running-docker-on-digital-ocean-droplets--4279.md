# 在数字海洋水滴上运行 Docker

> 原文：<https://dev.to/stevenanthonyrevo/running-docker-on-digital-ocean-droplets--4279>

你有没有想过搬到码头区做些开发工作？尝试在 docker 和 portainer 的帮助下建立一个容器化的网站。使用一些最新的 2018 开发人员工具，从您的网站中获得必要的定制。

首先，你需要在数字海洋中设置你的 droplet。使用此链接获得免费的 [$10](https://m.do.co/c/c81cb396ae03) 美元。一旦你选择了一个液滴位置，选择你的液滴大小。建议每月至少 20 美元。一旦你有了你的 ip 地址，并成功地开始运行你的 droplet。

导航到您的 [Portainer](https://github.com/portainer/portainer) 并使用他们的 docker 设置获取最新副本。

其次，确保你的 droplet 上安装了 docker。使用最新的 docker 安装，并确保通过“apt-get”命令更新您的系统。

通过以下方式在开放端口运行 portainer 设置:

$ dock run-d-p 9000:9000-restart always-v/var/run/docker . sock:/var/run/docker . sock-v/opt/portabler:/data portabler/portabler

恭喜你！这个命令将设置您的容器化环境，并让您在 portainer 的帮助下开始在容器中运行网站。

请记住保持 portainer 端口打开并在 droplet 中运行，否则您将无法访问 Portainer 仪表板。

干杯！快乐的开发人员，几天！