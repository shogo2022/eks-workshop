---
title: "EKS CloudWatch Container Insights"
chapter: true
weight: 250
tags:
  - intermediate
  - operations
  - monitoring
  - CON206
---

<!--
In this chapter we will learn and leverage the new CloudWatch Container Insights to see how you can use native CloudWatch features to monitor your EKS Cluster performance.
-->
この章では、新しいCloudWatch Container Insightsを使って、EKSクラスタのパフォーマンスをCloudWatchの機能で監視する方法を見ていきます。

<!--
You can use CloudWatch Container Insights to collect, aggregate, and summarize metrics and logs from your containerized applications and microservices. Container Insights is available for Amazon Elastic Container Service, Amazon Elastic Kubernetes Service, and Kubernetes platforms on Amazon EC2. The metrics include utilization for resources such as CPU, memory, disk, and network. Container Insights also provides diagnostic information, such as container restart failures, to help you isolate issues and resolve them quickly.
-->
CloudWatch Container Insightsは、コンテナ化されたアプリケーションやマイクロサービスからメトリクスやログを収集、集計、集約します。Container InsightsはAmazon Elastic Container Service、 Amazon Elastic Kubernetes Service、そしてKubernetes platforms on Amazon EC2で使うことができます。メトリクスには、CPU、メモリ、ディスク、そしてネットワークの使用率が含まれています。Container Insightsは、コンテナ再起動失敗などの分析情報を提供し、問題の特定と早急な復旧を助けます。

<!--
{{% notice note %}}
In order to complete this lab you will need to have a working EKS Cluster, With `Helm` installed.
You will need to have completed the [Start the Workshop...](/020_prerequisites/) through  [Launching your cluster with Eksctl](/030_eksctl/) and [Install Helm CLI](/beginner/060_helm/helm_intro/install/) as well.
{{% /notice %}}
-->
{{% notice note %}}
このハンズオンを完了させるためには、 `helm` がインストールされたEKSクラスタとが必要です。
[ワークショップの始め方...](/020_prerequisites/)から[eksctlを使って起動する](/030_eksctl/)、そして[Helm CLIのインストール](/beginner/060_helm/helm_intro/install/)を完了させておいてください。
{{% /notice %}}

![alt text](/images/ekscwci/insights.png "CW Insights")
