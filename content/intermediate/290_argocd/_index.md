---
title: "ArgoCDを使った継続的デプロイメント"
chapter: true
weight: 260
draft: false
tags:
  - intermediate
  - cd
  - gitops
---

<!--
# Continuous Deployment with ArgoCD
[Argo CD] (https://argoproj.github.io/argo-cd/) is a declarative, GitOps continuous delivery tool for Kubernetes. 
The core component of Argo CD is the Application Controller, which continuously monitors running applications and compares the live application state against the desired target state defined in the Git repository. This powers the following use cases:
-->
# ArgoCDを使った継続的デプロイメント
[Argo CD](https://argoproj.github.io/argo-cd/)は、宣言的なKubernetes用GitOpsの継続的デリバリツールです。
ArgoCDの核はアプリケーションコントローラで、稼働中のプリケーションを監視し、実アプリケーションの状態とGitレポジトリに定義された状態を比較していて、次のように使われます。

<!--
**Automated deployment** : controller pushes the desired application state into the cluster automatically, either in response to a Git commit, a trigger from CI pipeline, or a manual user request.
-->
**自動化されたデプロイメント** : GitコミットやCIパイプライン、ユーザからのマニュアルリクエストなどをトリガーとして、コントローラがアプリケーションの最新の状態をpushします

<!--
**Observability** : developers can quickly find if the application state is in sync with the desired state. Argo CD comes with a UI and CLI which helps to quickly inspect the application and find differences between the desired and the current live state.
-->
**視認性** : 開発者はアプリケーションが望ましい状態かをすぐに確認できます。ArgoCDはアプリケーションを確認し、現在の状態と望ましい状態の差異を確認するためのUIとCLIがあります。

<!--
**Operation** : Argo CD UI visualizes the entire application resource hierarchy, not just top-level resources defined in the Git repo. For example, developers can see ReplicaSets and Pods produced by the Deployment defined in Git. From the UI, you can quickly see Pod logs and the corresponding Kubernetes events. This turns Argo CD into very powerful multi-cluster dashboard.
-->
**運用** : ArgoCDのUIはGitレポジトリに定義されたトップレベルのリソースだけではなく、アプリケーション全体の階層構造を可視化します。例として、Gitで定義されているdeploymentによって生成されたReplicaSetとPodを確認することができます。UIからは、Kubernetesのイベントログに関連したPodのログをみることができます。これによってArgoCDは複数クラスタのダッシュボードを備えた強力なツールになっています。

![Argo Logo](/images/argo-logo.png)


