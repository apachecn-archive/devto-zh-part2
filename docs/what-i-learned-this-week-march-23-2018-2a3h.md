# 本周(2018 年 3 月 23 日)我学到了什么

> 原文：<https://dev.to/jrop/what-i-learned-this-week-march-23-2018-2a3h>

这周的清单会短一点，因为这周我被很多行政任务缠住了。

## Docker:服务与堆栈

在 Swarm 模式下使用 Docker 时，一个栈描述了在集群中运行的多个“服务”。正如`docker-compose`对于`docker run`一样，Docker 栈对于 Docker 服务也是如此。顺便说一句，栈也通过 YML 以非常相似的方式被描述，就像你在`docker-compose.yml`文件中一样。

Docker 服务是同构容器的集合，即具有相同的`image:tag`。Docker Swarm 将根据您设置的规则在多个节点上复制服务。