---
title: "StatefulSetsを使ったステートフルなコンテナ"
chapter: true
weight: 170
tags:
  - beginner
  - CON206
---

<!--
# Stateful containers using StatefulSets
-->
# StatefulSetsを使ったステートフルなコンテナ

<!--
[StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods, suitable for applications that require one or more of the following.
* Stable, unique network identifiers
* Stable, persistent storage
* Ordered, graceful deployment and scaling
* Ordered, automated rolling updates
-->
[StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)はPodのデプロイとスケーリングを管理し、これらpodの順番と一意性を保証するので、以下のいずれかを必要とするアプリケーションにむいています。
* 安定した、一意のネットワーク識別子
* 安定した、永続的なストレージ
* 規則に従ったグレースフルなデプロイメントとスケーリング
* 規則に従ったローリングアップデートの自動化

<!--
In this Chapter, we will review how to deploy MySQL database using `StatefulSet` and `Amazon Elastic Block Store` (EBS) as [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). The example is a MySQL single leader topology with multiple followers running asynchronous replication.
-->
この章では、`StatefulSet` とPersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)として `Amazon Elastic Block Store` (EBS)を使い、MySQLデータベースをデプロイします。この例は、一つのMySQLリーダーと、非同期のレプリケーションが走っている複数のフォロワーで構成されています。