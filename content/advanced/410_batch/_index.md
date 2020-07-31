---
title: "Argo Workflowを使ったバッチ処理"
date: 2018-11-18T00:00:00-05:00
weight: 410
draft: false
tags:
  - advanced
  - batch
  - CON205
---

<!--
### Batch Processing
-->
### バッチ処理

<!--
In this Chapter, we will deploy common batch processing scenarios using Kubernetes and [Argo](https://argoproj.github.io/).
-->
この章では、Kubernetesと[Argo](https://argoproj.github.io/)を使った、典型的なバッチ処理をデプロイします。

![Argo Logo](/images/argo-workflow/argo-logo.png)

<!--
#### What is Argo?
-->
#### Argoとは?

<!--
Argo Workflows is an open source container-native workflow engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD (Custom Resource Definition).
-->
Argo WorkflowはKubernetes上の並行ジョブを管理するためのオープンソースなコンテナネイティブワークフローエンジンです。Argo WOrkflowはKubernetesのCRD(Custom Resource Defition)として実装されます。

<!--
* Define workflows where each step in the workflow is a container.
* Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a directed acyclic graph (DAG).
* Easily run compute intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes.
* Run CI/CD pipelines natively on Kubernetes without configuring complex software development products.
-->
* コンテナからなるワークフローの定義
* 複数ステップのワークフローは、連続したタスクとして作成するか、directed acyclic graph(DAG、有向非巡回グラフ)を使ってタスク間の依存性を確認
* Kubernets上のArgo Workflowで機械学習やデータ処理などの重い処理を簡単に実行
* ソフトウェア開発のための複雑な設定をすることなく、Kubernetes上でネイティブなCI/CDパイプラインの実行

<!--
Argo is a [Cloud Native Computing Foundation (CNCF)](https://cncf.io/) hosted project.
-->
Argoは[Cloud Native Computing Foundation (CNCF)](https://cncf.io/)が主宰するプロジェクトです。