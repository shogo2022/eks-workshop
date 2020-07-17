---
title: "CodePipelineでCI/CD"
chapter: true
weight: 220
draft: false
tags:
  - advanced
  - operations
  - ci/cd
  - CON205
---

<!--
# CI/CD with CodePipeline
-->
# CodePipelineでCI/CD

<!--
[Continuous integration](https://aws.amazon.com/devops/continuous-integration/) (CI) and [continuous delivery](https://aws.amazon.com/devops/continuous-delivery/) (CD)
are essential for deft organizations. Teams are more productive when they can make discrete changes frequently, release those changes programmatically and deliver updates
without disruption.
-->
[継続的インテグレーション(Continuous integration)](https://aws.amazon.com/devops/continuous-integration/) (CI) と [継続的デリバリー(continuous delivery)](https://aws.amazon.com/devops/continuous-delivery/) (CD)
は企業に不可欠です。チームは個別の変更を頻繁にでき、変更のリリースをプログラムで管理し、影響なくアップデートができる環境では生産力があがります。

<!--
In this module, we will build a CI/CD pipeline using [AWS CodePipeline](https://aws.amazon.com/codepipeline/). The CI/CD pipeline will deploy a sample Kubernetes service,
we will make a change to the GitHub repository and observe the automated delivery of this change to the cluster.
-->
このモジュールでは、[AWS CodePipeline](https://aws.amazon.com/codepipeline/)を使い、CI/CDパイプラインをビルドします。CI/CDパイプラインはサンプルのKubernetesサービスをデプロイし、Githubレポジトリに変更を加えて、その変更が自動的にクラスタに適用されるのを観察します。