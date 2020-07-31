---
title: "紹介"
date: 2018-11-18T00:00:00-05:00
weight: 10
draft: false
---

<!--
Batch processing refers to performing units of work, referred to as a `job` in a repetitive and unattended fashion. Jobs are typically grouped together and processed in batches (hence the name).
-->
バッチ処理とは一定のまとまりの仕事を実行することで、連続していて人が介在する必要なく行われる `job` を指しています。

<!--
Kubernetes includes native support for [running Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/). Jobs can run multiple pods in parallel until receiving a set number of completions. Each pod can contain multiple containers as a single unit of work.
-->
[Jobの実行](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)はKubernetesでネイティブにサポートされています。Jobは設定された回数が完了するまで並行で複数のpodを実行することができます。

<!--
Argo enhances the batch processing experience by introducing a number of features:
-->
Argoは様々な機能でバッチ処理を拡張します:

<!--
* Steps based declaration of workflows
* Artifact support
* Step level inputs & outputs
* Loops
* Conditionals
* Visualization (using Argo Dashboard)
* ...and more
-->
* 宣言的な手順に基づくワークフロー
* アーティファクトのサポート
* 手順ごとの入力と出力
* ループ
* 条件
* 可視化(Argp Dashboard)
* ...その他

<!--
In this module, we will build a simple Kubernetes Job, recreate that job in Argo, and add common features and workflows for more advanced batch processing.
-->
このモジュールでは、シンプルなKubernetes Jobを作成し、そのjobをArgoで再作成し、その後に複雑なバッチ処理で典型的な機能やワークフローを追加します。