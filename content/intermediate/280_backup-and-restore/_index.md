---
title: "Veleroを使ったバックアップとリストア"
chapter: true
weight: 280
draft: false
tags:
  - intermediate
  - operations
---

<!--
# Backup and Restore EKS using Velero
-->
# Veleroを使ったEKSのバックアップとリストア

<!--
[Velero](https://velero.io/) (formerly Heptio Ark) is an open source tool to safely backup and restore, perform disaster recovery, and migrate Kubernetes cluster resources and persistent volumes. 
-->
[Velero](https://velero.io/)(以前はHeptio Ark)はKubernetesクラスタリソースと永続ボリュームを安全にバックアップやリストア、災害復旧、そして移行をするためのオープンソースのツールです。

<!--
Velero enables you to create on-demand and scheduled backups. When a backup or restore command is executed using velero cli, a custom resource, defined by a Kubernetes Custom Resource Definition (CRD), is created and stored in etcd. Velero controllers notice the new object created and processes the custom resources and performs the needed operation like backups, restores, and other related operations.
-->
Veleroではオンデマンドもしくはスケジュールでバックアップができます。Velero cliでバックアップやリストアのコマンドが発行されると、kubernetes CRDのカスタムリソースが作成され、etcdに保存されます。Veleroコントローラは新しいオブジェクトが確認されると、そのカスタムリソースを処理して、バックアップやリストアなどそれぞれのオペレーションを実行します。

<!--
Velero let's you backup your entire cluster or namespace(s) or filter objects by using labels. Velero helps with migrating your on-prem Kubernetes workloads to cloud, cluster upgrades and disaster recovery.
-->
Veleroはクラスタ全体もしくは名前空間、もしくはラベルでフィルタをしたもののみをバックアップすることもできます。VeleroはオンプレミスのKubernetesワークロードをクラウドに移行したり、クラスタのアップグレードや災害復旧に使用できます。

<!--
In this module, you will learn how to backup and restore an EKS cluster using Velero. 
-->
このモジュールでは、Veleroを使ったEKSクラスタのバックアップとリストアを見ていきます。

![Title Image](/images/backupandrestore/velero.png)
