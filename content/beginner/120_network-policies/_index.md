---
title: "ネットワークポリシーによるクラスタの保護"
chapter: true
weight: 120
tags:
  - intermediate
---

<!--
# Securing your cluster with network policies
-->
# ネットワークポリシーによるクラスタの保護

<!--
In this chapter, we are going to use two tools to secure our cluster by using network policies and then integrating our cluster's network policies with EKS security groups.
-->
このチャプターでは、ネットワークポリシーでクラスタを保護し、EKSのセキュリティグループとネットワークポリシーの連携をさせるために2つのツールを使います。

<!--
First we will use [Project Calico](https://www.projectcalico.org) to enforce Kubernetes network policies in our cluster, protecting our various microservices.
-->
まずは[Project Calico](https://www.projectcalico.org)を使い、Kubernetesのネットワークポリシーを実装し、様々なマイクロサービスを保護します。

<!--
After that, we will use [Calico Enterprise](https://www.tigera.io/tigera-products/calico-enterprise) to
- Implement Egress Access Controls to enable your EKS workloads to communicate with other Amazon services (for example: RDS or EC2 instances) or other API endpoints.
- Troubleshoot microservices that are unable to communicate with each other
- Implement and report on Enterprise Security Controls in EKS
-->
その後、[Calico Enterprise](https://www.tigera.io/tigera-products/calico-enterprise)を使い、
- EKSのワークロードが他のAmazonサービス(例としてRDSやEC2インスタンス)や他のAPIエンドポイントとやりとりができるように、外向けのアクセス制御を実装します。
- マイクロサービス間の通信不能問題の解決をします。
- EKS内のエンタープライズセキュリティコントロールの実装とレポートの出力をします。

![calico](/images/Project-Calico-logo-1000px.png)

![calico Enterprise](/images/Calico-enterprise-logo-1000px.png)
