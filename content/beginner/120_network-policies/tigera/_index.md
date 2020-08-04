---
title: "Calico Enterpriseを使ったネットワークセキュリティと可視化"
weight: 20
draft: true
hidden: true
---

<!--
### CALICO ENTERPRISE USE CASES
-->
### CALICO ENTERPRISEのユースケース

<!--
In this module, we will use Tigera’s [Calico Enterprise](https://www.tigera.io/tigera-products/calico-enterprise) to implement the following 3 Amazon EKS use cases:
-->
このモジュールでは、Tigeraの[Calico Enterprise](https://www.tigera.io/tigera-products/calico-enterprise)を使って次の3つのEKSユースケースで実装します:

<!--
### 1) Implement Egress Access Controls
-->
### 1) 出力方向アクセス制御(Egress Access Control)の実装

<!--
   Establishing service to service connectivity within your Amazon EKS cluster is easy. But how do you enable some of your workloads to securely connect to services like Amazon RDS, ElasticCache, etc. that are external to the cluster.
-->
   Amazon EKSクラスタ内でのservice間通信を建てつけるのは簡単です。しかしクラスタ外のもの、例えばAmazon RDSやElastiCacheなどのサービスにセキュアに接続させるにはどうしたら良いでしょうか。

<!--
   With Calico Enterprise, you can author DNS Policies that implement fine-grained access controls between a workload and the external services it needs to connect to.
-->
   Calico EnterpriseではDNSポリシーを使い、ワークロードと接続先の外部サービスのきめ細かなアクセス制御を実装できます。

<!--
### 2) Troubleshoot Microservice Connectivity
-->
### 2) マイクロサービス間の接続障害対応

<!--
   Troubleshooting microservice connectivity issues can be incredibly difficult due to the dynamic nature of container orchestration in Amazon EKS. Connectivity issues can be related to network performance, but often are the result of your Network Policies not being defined properly.
-->
   Amazon EKSのコンテナオーケストレーションは動的なので、マイクロサービスの接続障害は非常に複雑です。接続障害はネットワークの性能によるところもありますが、ネットワークポリシーが適切に定義されていないことが多いです。

<!--
   With Calico Enterprise, you can explore a visual representation of all your microservices communicating with each other, and filter by connections that were accepted and denied. You can further drill into an individual denied connection to see which Network Policy evaluated and denied the connection, and then fix the policy to resolve the connectivity issue.
-->
   Calico Enterpriseでは、マイクロサービス間の通信を可視化でき、許可された通信や拒否された通信でフィルターできます。拒否された通信を個々に見ることもでき、どのネットワークポリシーが通信を拒否したかを確認し、接続障害を解消させることができます。

<!--
### 3) Implement Enterprise Security Controls
-->
### 3) エンタープライズセキュリティコントロールの実装

<!--
   Many applications have compliance requirements such as workload isolation, ensuring dev cannot talk to prod or implementing network zones (e.g. DMZ can communicate with the public internet but not your backend databases). While you can implement these rules using open-source Project Calico, there are a few limitations:
  - It is possible for other users to inadvertently override or intentionally tamper with your security controls, and very difficult to detect when that happens.
  - Proof that the policies have been enforced now and in the past is not possible
-->
   多くのアプリケーションは、開発環境が商用環境と通信ができないようにしたり、ネットワークゾーンの実装(例 DMZはインターネットと通信できるがバックエンドにはできない)などワークロードの分離などの規制に沿う必要があります。オープンソースのCalicoでこれらを実装することもできますが、いくつか制限事項があります:
  - 他のユーザが間違って上書きしてしまったり、故意にセキュリティ制御を改ざんすることが可能で、それらを検知することが大変困難です。
  - 現在および過去にポリシーが強制されていた証跡がない
 
<!--
With Calico Enterprise, you can implement Security Controls at a higher precedent policy tier that cannot be overridden by other users. You will also learn how to provide audit reports that demonstrate compliance now and historically, as well as audit (or alert on) changes to policies.
-->
Calico Enterpriseでは、他のユーザが上書きできないように高い優先順位でセキュリティ制御を実装することができます。また、現在及び過去の規制に関する監査レポートの出力方法と、ポリシーが変更された際の監査(およびアラート)の設定方法を学びます。
