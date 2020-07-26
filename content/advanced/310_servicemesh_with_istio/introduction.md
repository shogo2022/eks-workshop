---
title: "紹介"
date: 2018-11-13T16:36:24+09:00
weight: 10
draft: false
---

{{% notice info %}}
<!--
This chapter has been updated to Istio 1.5.x.
[Click here](https://istio.io/news/releases/1.5.x/announcing-1.5/) to know more about this new release.
-->
この章はIstio 1.5.x用にアップデートされています。
このリリースについての詳しい情報は[こちら](https://istio.io/news/releases/1.5.x/announcing-1.5/)をクリックしてください。
{{% /notice %}}

<!--
Istio is a completely open source service mesh that layers transparently onto existing distributed applications. It's also a platform, including APIs, that let it integrate into any logging platform, or telemetry or policy system.
-->
Istioはオープンソースのサービスメッシュで、既存の分散アプリ上に透過的に設置できます。また、どんなロギングシステム、テレメトリポリシーシステムにm連携できるプラットフォームで APIも備えています。

<!--
Istio works by having a small network proxy sit alongside each microservice called "sidecar".
It's role is to intercept all of the service’s traffic, and handles it more intelligently than a simple layer 3 network can. [Envoy proxy](https://www.envoyproxy.io/) is used as the sidecar and was originally written at Lyft and is now a CNCF project.
-->
Istioはそれぞれのマイクロサービスに"サイドカー"と呼ばれるプロキシを配置します。
それは全トラフィックを中継し、単純なレイヤ3のネットワークよりインテリジェントに処理します。元はLyftによって書かれ、のちにCNCFプロジェクトになった[Envoyプロキシ](https://envoyproxy.io/)がサイドカーとして使われています。

<!--
An Istio service mesh is logically split into a data plane and a control plane.
-->
Istioサービスメッシュは論理的にデータプレーンとコントロールプレーンに分離されます。

<!--
* The **data plane** is composed of a set of intelligent proxies (Envoy) deployed as sidecars. These proxies mediate and control all network communication between microservices. They also collect and report telemetry on all mesh traffic.
* The **control plane** manages and configures the proxies to route traffic.
-->
* **データプレーン** はサイドカーとしてデプロイされるインテリジェントなプロキシ(Envoy)です。このプロキシは汎用のポリシーおよびテレメトリのハブであるMixerと一緒にマイクロサービス間のコミュニケーションを制御し仲介しています。
* **コントロールプレーン** は通信を制御するためにプロキシを管理、設定します。

<!--
The following diagram shows the different components that make up each plane:
-->
次の構成図はそれぞれの層を作る構成要素を表しています:

![Istio Architecture](/images/istio/istio_architecture.svg)

* <span style="color:orange">**Envoy Proxy**</span>
<!--
  * Processes the inbound/outbound traffic from inter-service and service-to-external-service transparently.
-->
  * サービス内やサービスと外部サービスの外向け/内向け通信を透過的に処理

* <span style="color:orange">**Pilot**</span>
<!--
  * Pilot provides service discovery for the Envoy sidecars, traffic management capabilities for intelligent routing (e.g., A/B tests, canary deployments, etc.), and resiliency (timeouts, retries, circuit breakers, etc.)
-->
  * Pilotは Envoyサイドカーにサービスディスカバリを提供したり、インテリジェントな通信管理(例 A/Bテスト、カナリアデプロイメントなど)、そして回復機能(タイムアウト、リトライ、サーキットブレーカーなど)を提供します。

* <span style="color:orange">**Citadel**</span>
<!--
  * [Citadel](https://istio.io/docs/concepts/security/) enables strong service-to-service and end-user authentication with built-in identity and credential management.
-->
  * [Citadel](https://istio.io/docs/concepts/security/)は組み込みの識別・認証機能でサービス間およびエンドユーザの強力な認証を可能にします。

* <span style="color:orange">**Galley**</span>
<!--
  * Galley is Istio’s configuration validation, ingestion, processing and distribution component. It is responsible for insulating the rest of the Istio components from the details of obtaining user configuration from the underlying platform (e.g. Kubernetes).
-->
  * GalleyはIstio設定を検証、処理、配布するコンポーネントで、下層のプラットフォーム(例 Kubernetes)から詳細なユーザ設定を取得し、他のIstioコンポーネントに配布します。