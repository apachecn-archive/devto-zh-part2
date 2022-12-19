# 使用 Grafana、Zipkin、Weavescope 和其他插件进行 Knative 监控..

> 原文：<https://dev.to/vidyasagarmsc/knative-monitoring-with-grafana-zipkin-weavescope--other-plugins-4g9o>

在这篇文章中，您将看到在上一篇文章 [*中发布在 IBM Cloud 上的一个名为 Knative-node-app 的 nodejs 应用程序的 Knative 和 Istio 的遥测方面，使用 Istio 安装 Knative 并在 IBM Cloud 上部署一个应用程序*](https://dev.to/vidyasagarmsc/install-knative-with-istio-on-ibm-cloud-2fd9) 。

根据 Knative 的监控、记录和跟踪安装文件，

> Knative Serving 提供了两种不同的监控设置: [Elasticsearch、Kibana、Prometheus 和 Grafana](https://github.com/knative/docs/blob/master/serving/installing-logging-metrics-traces.md#elasticsearch-kibana-prometheus--grafana-setup) 或 [Stackdriver、Prometheus 和 Grafana](https://github.com/knative/docs/blob/master/serving/installing-logging-metrics-traces.md#stackdriver-prometheus--grafana-setup) 您只能安装这两种设置中的一种，不支持这两种设置并行安装。

我们将坚持使用 Elasticsearch、Kibana、Prometheus 和 Grafana stack，并将额外使用 Weavescope 对集装箱、豆荚等进行深度可视化。,

如果在设置 Knative 时安装了服务组件，则应该已经安装了监视组件。要确认有效的服务组件安装，运行以下命令

```
$ kubectl describe deploy controller --namespace knative-serving 
```

<figure>[![](../Images/e9ed92cfa3ae6f56022393b5ac52f0ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2B-hRy---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdMS_nwLCgnPozse2FRUpyg.png) 

<figcaption>开胃小菜</figcaption>

</figure>

要检查监控组件的安装，运行以下命令

```
kubectl get pods --namespace knative-monitoring 
```

[![](../Images/5058cb441d2d4bc12127c3388378fbb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dki2k-2T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aw9v0J3mPNewj16F2DihEtg.png)

> 如果您没有看到任何正在运行的东西，请按照这里的[步骤](https://github.com/knative/docs/blob/master/serving/installing-logging-metrics-traces.md#before-you-begin)进行设置。

### 格拉法纳

您可以通过 [Grafana](https://grafana.com/) 用户界面访问指标。Grafana 是[普罗米修斯](https://prometheus.io/)的可视化工具。

要打开 Grafana，请输入以下命令:

```
kubectl port-forward --namespace monitoring $(kubectl get pods --namespace monitoring --selector=app=grafana --output=jsonpath="{.items..metadata.name}") 3000 
```

**注意:**这将在端口 3000 上启动 Grafana 的本地代理。出于安全原因，Grafana UI 只在集群中公开。

导航到位于 [http://localhost:3000](http://localhost:3000/) 的 Grafana UI。

[![](../Images/81d0c15e1b0031478279ab13acdc8c28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6mgZm56r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGSpmymhThc2bJeauSltYhA.png)

<figure>[![](../Images/16391f052b38336f9990773249433666.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--54zgay5B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyeAs4iWTkNXLtxB-8IBPTg.png)

<figcaption>Grafana UI—knative-node-app</figcaption>

</figure>

您还可以检查 Knative Serving- scaling、deployments、pods 等指标。,

<figure>[![](../Images/5fd2962038ac2e595b613f4f62184667.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cUTLbmlL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AC59TynesT42mG-cUJv4kcQ.png) 

<figcaption></figcaption>

</figure>

以下仪表板预装了 Knative Serving:

*   修订版 HTTP 请求:每个修订版和每个配置的 HTTP 请求计数、延迟和大小指标
*   节点:节点级别的 CPU、内存、网络和磁盘指标
*   pod:pod 级别的 CPU、内存和网络指标
*   部署:在部署级别聚合 CPU、内存和网络指标
*   Istio、混合器和导频:详细的 Istio 网格、混合器和导频指标
*   Kubernetes:仪表板提供对集群运行状况、部署和容量使用情况的洞察

### 齐普金

为了访问请求跟踪，您需要使用 Zipkin 可视化工具。

要打开 Zipkin UI，请输入以下命令:

```
kubectl proxy 
```

该命令在端口 8001 上启动 Zipkin 的本地代理。出于安全原因，Zipkin UI 只在集群中公开。

导航到 [Zipkin UI](http://localhost:8001/api/v1/namespaces/istio-system/services/zipkin:9411/proxy/zipkin/) ，点击“查找痕迹”查看最新的痕迹。您可以搜索跟踪 ID 或查看特定应用程序的跟踪。单击跟踪可查看特定呼叫的详细视图。

[![](../Images/55e8f0c15b4b85e4ee942e2ecb903bea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xz8NpBxl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJ4ZCAZX5c0rD_n97yTaMZA.png)

<figure>[![](../Images/ff9497b65ceec2f9ad2446893360b27a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ms9QxF_J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOX1kUKfK5-yNKwsOry43Pg.png) 

<figcaption>齐普金痕迹— Knative-node-app-0001</figcaption>

</figure>

### 织镜

当[使用 Istio](https://vmacwrites.wordpress.com/2018/06/28/obtain-and-visualise-uniform-metrics-logs-traces-across-microservices-using-istio/) 获得并可视化跨微服务的统一指标、日志、踪迹时，我爱上了 [Weavescope](https://www.weave.works/oss/scope/) 。所以我想到用它来理解我的应用程序中涉及的进程、容器、主机和其他内容。

使用命令
将 Scope 部署到 Kubernetes 集群上

```
kubectl apply -f “[https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl](https://cloud.weave.works/k8s/scope.yaml?k8s-version=%24(kubectl) version | base64 | tr -d ‘\n’)” 
```

要打开 Weavescope，运行命令并打开[http://localhost:4040/](http://localhost:4040/)

```
kubectl port-forward -n weave “$(kubectl get -n weave pod — selector=weave-scope-component=app -o jsonpath=’{.items..metadata.name}’)” 4040 
```

[![](../Images/25676b97f1e02b8b0e1140cf0801bb0b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---n6st57u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6azgps1dDbeLcNHqepNH7A.png)

[![](../Images/6ead98324772fa226bc599788c76c304.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v_ErrI1P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhsXkyaiJNbtGR4UcMd73-Q.png)

### Kibana + ElasticSearch

我试图使用 Kibana UI(用于 [Elasticsearch](https://info.elastic.co/) 的可视化工具)来可视化日志，但是在配置索引模式时遇到了以下错误— *“无法获取映射。你有符合这种模式的指数吗？”*

[![](../Images/d4a7097f109957973b6b7ac395bb1ece.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wcSxeTe4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbOQ9W4aXu3vb5iMiQt1oMA.png)

由于 knative GitHub repo 上的[这一期](https://github.com/knative/docs/issues/447)将对“记录和监控”相关主题进行修订。我将来肯定会重新访问日志。

**更新:**根据本 [stackoverflow 问题](https://stackoverflow.com/questions/53512182/knative-logging-kibana-unable-to-fetch-mapping)中的答案找到了此问题的解决方法。

以下是步骤，

运行以下命令以应用补丁来修复 fluentd-ds pod 不显示问题

```
kubectl apply -f [https://raw.githubusercontent.com/gevou/knative-blueprint/master/knative-serving-release-0.2.2-patched.yaml](https://raw.githubusercontent.com/gevou/knative-blueprint/master/knative-serving-release-0.2.2-patched.yaml) 
```

验证您的每个节点都有 beta.kubernetes.io/fluentd-ds-ready=true 标签:

```
kubectl get nodes --selector beta.kubernetes.io/fluentd-ds-ready=true 
```

如果您收到“未找到资源”响应:

*   运行以下命令，确保 Fluentd DaemonSet 在所有节点上运行:

```
kubectl label nodes — all beta.kubernetes.io/fluentd-ds-ready=”true” 
```

*   运行以下命令，确保 fluentd-ds daemonset 至少在一个节点上准备就绪:

```
kubectl get daemonset fluentd-ds --namespace knative-monitoring 
```

[![](../Images/641a34f94c309235608b589108565533.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hc9Roche--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApZSzO_sKo4q9yjNs8EWj5Q.png)

等待一会儿，运行该命令

```
kubectl proxy 
```

*   导航到[基巴纳用户界面](http://localhost:8001/api/v1/namespaces/knative-monitoring/services/kibana-logging/proxy/app/kibana)。*代理可能需要几分钟才能工作*。
*   在“配置索引模式”页面中，输入 logstash-*来索引模式，并从时间过滤器字段名称中选择@timestamp，然后单击 Create 按钮。
*   要创建第二个索引，请选择页面左上角的创建索引模式按钮。输入 zipkin*来索引模式，并从时间过滤器字段名称中选择 timestamp_millis，然后单击创建按钮。

[![](../Images/581b4d5d79e709ba5ad38a30716ef4c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--22E146aR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbN4O7ds87NnpaXmQGBLhEw.png)

如果你仍然看到这个问题，试着点击**基巴纳**页面上的**开发工具**并运行这个命令

```
GET \_cat/indices?v 
```

[![](../Images/0eaab0066b8a4eb16b381591f466be88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W3ba-hTb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Akk7Gvn-q84O6nEf8aWB6ow.png)

### 进一步阅读

*   **Knative** [用 Sysdig](https://medium.com/vmacwrites/knative-monitoring-with-sysdig-on-ibm-cloud-dc206c3f413d) 监控
*   **Knative 2/2**——【https://medium.com/google-cloud/knative-2-2-e542d71d531d T2】
*   **使用 Istio**—[https://github . com/IBM-Cloud/jpetstore-kubernetes/tree/master/Istio](https://github.com/IBM-Cloud/jpetstore-kubernetes/tree/master/istio)获得并可视化不同服务的统一指标、日志和跟踪

* * *