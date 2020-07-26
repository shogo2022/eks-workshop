---
title: "Istioを使ったサービスメッシュ"
date: 2018-11-13T16:32:30+09:00
weight: 310
draft: false
tags:
  - advanced
  - operations
  - servicemesh
---

![Service Mesh Architecture](/images/istio/istio-intro1.png)

<!--
A service mesh is a dedicated infrastructure layer for handling **service-to-service communication**. It’s responsible for the reliable delivery of requests through the complex topology of services that comprise a modern, cloud native application.
-->
サービスメッシュは **サービス間のコミュニケーション** を管理するためのレイヤーです。近代的でクラウドネイティブなアプリケーションを構成するサービスの複雑なつながりの上を行き交うリクエストの、安定した伝達を担っています。

<!--
Service mesh solutions have two distinct components that behave somewhat differently:
-->
サービスメッシュは性質の異なる2つのコンポーネントからできています:

<!--
* The <span style="color:orange">**data plane**</span> is composed of a set of intelligent proxies (Envoy) deployed as sidecars. These proxies mediate and control all network communication between microservices along with Mixer, a general-purpose policy and telemetry hub.
* The <span style="color:orange">**control plane**</span> manages and configures the proxies to route traffic. Additionally, the control plane configures Mixers to enforce policies and collect telemetry.
-->
* <span style=="color:orange">**データプレーン**</span> はサイドカーとしてデプロイされるインテリジェントなプロキシ(Envoy)です。これらのプロキシは、汎用のポリシーおよびテレメトリのハブであるMixerと一緒にマイクロサービス間のコミュニケーションを制御し仲介しています。
* <span style="color:orange">**コントロールプレーン**</span> は通信を制御するためにプロキシを管理、設定します。また、コントロールプレーンはMixersを設定し、ポリシーを強制したり、テレメトリを収集したりもします。