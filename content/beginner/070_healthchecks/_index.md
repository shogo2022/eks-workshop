---
title: "ヘルスチェック"
chapter: true
weight: 70
tags:
  - beginner
  - CON206
---

<!--
# Health Checks
-->
# ヘルスチェック

<!--
By default, Kubernetes will restart a container if it crashes for any reason. It uses Liveness and Readiness probes which can be configured for running a robust application by identifying the healthy containers to send traffic to and restarting the ones when required.
-->
Kubernetesは何らかの理由でコンテナがクラッシュした場合は、それを再起動するようになっています。
それにはライブネス、レディネスプローブが使われており、それによってコンテナの状態を識別し、必要な時には再起動をしたり頑強なアプリケーションを実行するための設定ができます。

<!--
In this section, we will understand how [liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) are defined and test the same against different states of a pod. Below is the high level description of how these probes work.
-->
このセクションでは、[ライブネス、レディネスプローブ](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)がどのように定義されているのかを理解し、異なる状況のpodに対してテストをします。以下はこれらプローブの概要です。

<!--
**Liveness probes** are used in Kubernetes to know when a pod is alive or dead. A pod can be in a dead state for a variety of reasons; Kubernetes will kill and recreate the pod when a liveness probe does not pass.
-->
**ライブネスプローブ(liveness probes)** はKubernetesがpodの生死確認に使います。podは様々な理由でdead状態になります; ライブネスプローブが成功しなかった場合、Kubernetesはpodをkillし、再作成します。

<!--
**Readiness probes** are used in Kubernetes to know when a pod is ready to serve traffic. Only when the readiness probe passes will a pod receive traffic from the service; if a readiness probe fails traffic will not be sent to the pod.
-->
**レディネスプローブ(readiness probes)** はpodがトラフィックを受け付ける状態になったかの判断に使われます。レディネスプローブが成功した時のみpodはserviceからのトラフィックを受け取ります; レディネスプローブが成功しない限りはpodにトラフィックが送られることはありません。

<!--
We will review some examples in this module to understand different options for configuring liveness and readiness probes.
-->
このモジュールではいくつかの例を通して、ライブネス、レディネスプローブの設定方法の違いについて理解を深めます。