---
title: "X-Rayによる追跡"
chapter: true
weight: 245
draft: false
tags:
  - advanced
  - operations
  - monitoring
  - CON205
---

<!--
# Tracing with X-Ray
-->
# X-Rayによる追跡

<!--
As [distributed systems](https://en.wikipedia.org/wiki/Distributed_computing) evolve, monitoring and debugging services becomes challenging. Container-orchestration platforms like Kubernetes solve a lot of problems, but they also introduce new challenges for developers and operators in understanding how services interact and where latency exists. [AWS X-Ray](https://aws.amazon.com/xray/) helps developers analyze and debug distributed services.
-->
[分散システム]](https://en.wikipedia.org/wiki/Distributed_computing)が進化するにつれて、監視とデバッグはより困難になりました。Kubernetesのようなコンテナオーケストレーションは多くの問題を解決しましたが、同時にサービスの前後関係の理解や遅延の発生場所などの困難を新たに開発者や運用者にもたらしました。[AWS X-Ray](https://aws.amazon.com/xray/)はそんな開発者のために分散サービスの分析とデバッグを助けます。

<!--
In this module, we are going to deploy the [X-Ray agent](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html) as a [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), deploy sample front-end and back-end services that are instrumented with the [X-Ray SDK](https://docs.aws.amazon.com/xray/index.html#lang/en_us), make some sample requests and then examine the traces and service maps in the AWS Management Console.
-->
このモジュールでは、[X-Rayエージェント](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html)を[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)としてデプロイし、[X-Ray SDK](https://docs.aws.amazon.com/xray/index.html#lang/en_us)が入っているサンプルのフロントエンドとバックエンドサービスをデプロイし、リクエストを送りAWS管理コンソールでトレースとサービスマップを確認します。

![GitHub Edit](/images/x-ray/overview.png)
