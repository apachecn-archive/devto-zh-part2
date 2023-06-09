# AWS 入门:开源研讨会

> 原文：<https://dev.to/tryolabs/getting-started-with-aws-open-source-workshop-757>

## 简介

我们在 Tryolabs 的优势之一是我们拥有来自不同技术背景的人。为了确保加入公司的每个人，不管他们以前的经验如何，都能够利用我们通常使用的堆栈快速开发应用程序，我们有一个广泛的入职流程，涉及到一个真实应用程序的开发(前端、后端和一些数据科学)，有一个教练，代码审查和迭代改进。

## 为什么要使用云服务？

一项有时被忽视的有趣技能(尤其是初级开发人员)是如何以健壮和可扩展的方式将应用程序投入生产。我们的应用程序最好是高度可用的(这意味着它们很少*停机*)，并且具有能够承受并发用户负载而不会崩溃或变慢的机制。

为此，我们使用云提供商，如[亚马逊网络服务(AWS)](https://aws.amazon.com/) 、[谷歌云平台](https://cloud.google.com/)或[微软 Azure](https://azure.microsoft.com/) 。每个提供商都提供自己的服务来简化我们应用的部署。例如，有些提供不同类型的数据库作为服务，因此我们不需要处理数据库管理或扩展。可扩展文件存储解决方案也很常见。

有趣的是，这些云提供商提供的服务会影响应用程序的设计(例如，您使用的数据库)。几年前非常难以开发的高度可扩展的应用程序现在几乎变得轻而易举。

## 如何入门 AWS？

因此，鉴于云提供商尤其是 AWS 的重要性，我们已经创建了一个指南，我们称之为 [AWS Workshop](https://github.com/tryolabs/aws-workshop) ，并且现在正在开源。

在这个研讨会中，开发人员将学习如何使用 AWS 堆栈中的几个可用服务来部署演示应用程序。作为要部署的演示应用程序，我们选择了一个名为 Conduit 的开源测试应用程序，它便于学习新的框架，因为同一应用程序在后端和前端的多个框架中都有实现。特别是，我们使用用 [React](https://reactjs.org/) 和[Django](https://www.djangoproject.com/)+[Django-Rest-Framework](http://www.django-rest-framework.org/)后端构建的版本，这与我们在几个项目中使用的技术非常相似。

在研讨会中，开发人员将学习:

1.  在您的 AWS 帐户中设置用户。
2.  在 [S3](https://aws.amazon.com/s3/) 部署网站，在 [EC2](https://aws.amazon.com/ec2/) 部署后端，在 [RDS](https://aws.amazon.com/rds/) 部署数据库。
3.  设置[负载均衡](https://aws.amazon.com/elasticloadbalancing/)和[自动扩展组](https://aws.amazon.com/autoscaling/)。
4.  设置一个 [VPC](https://aws.amazon.com/vpc/) 和一个[堡垒实例](https://docs.aws.amazon.com/quickstart/latest/linux-bastion/architecture.html)。
5.  使用 [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/) 部署应用程序。

> 你是 AWS 的新用户，想使用它的基本服务来部署你的应用吗？从这里开始参加开源研讨会吧[！](https://github.com/tryolabs/aws-workshop)

未来，我们将添加其他服务，例如使用 [AWS Lambda](https://aws.amazon.com/lambda/) 的部署(欢迎拉请求！)

本文原载[此处](https://tryolabs.com/blog/2018/06/21/getting-started-with-aws-open-source-workshop/)。