---
title: "Elasticsearch、Fluent Bit、Kibana (EFK)でログ取得"
chapter: true
weight: 230
tags:
  - intermediate
  - operations
  - logging
  - CON206
---

<!--
# Implement Logging with EFK
-->
# EFKでログ環境構築

<!--
In this Chapter, we will deploy a common Kubernetes logging pattern which consists of the following:
-->
この章では、次のものを使って一般的なKubernetesログ環境パターンを構築します:

<!--
* [Fluent Bit](https://fluentbit.io/): an open source and multi-platform Log Processor and Forwarder which allows you to collect data/logs from different sources, unify and send them to multiple destinations. It's fully compatible with Docker and Kubernetes environments.
-->
* [Fluent Bit](https://fluentbit.io/): オープンソースでマルチプラットフォームなログプロセッサーおよびフォワーダで、異なるソースからのデータおよびログの収集と、それらの統合、複数の宛先への送信を可能にします。DockerとKubernetesに互換性があります。

<!--
* [Amazon Elasticsearch Service](https://aws.amazon.com/elasticsearch-service/): a fully managed service that makes it easy for you to deploy, secure, and run [Elasticsearch](https://www.elastic.co/what-is/elasticsearch) cost effectively at scale.
-->
* [Amazon Elasticsearch Service](https://aws.amazon.com/elasticsearch-service/): 費用対効果が高くスケーラブルな[Elasticsearch](https://www.elastic.co/what-is/elasticsearch)を、簡単にデプロイ、保護、実行できるフルマネージドサービスです。

<!--
* [Kibana](https://www.elastic.co/what-is/kibana): an open source frontend application that sits on top of the Elasticsearch, providing search and data visualization capabilities for data indexed in Elasticsearch.
-->
* [Kibana](https://www.elastic.co/what-is/kibana): Elasticsearchの上で動くオープンソースのフロントエンドアプリケーションで、Elasticsearch上のインデックスされたデータを検索、可視化を提供します。

<!--
Together, Fluent Bit, Elasticsearch and Kibana is also known as "EFK stack".
-->
Fluentd、Elasaticsearch、そしてKibanaをまとめて、"EFKスタック"としても知られています。

<!--
Fluent Bit will forward logs from the individual instances in the cluster to a centralized logging backend where they are combined for higher-level reporting using ElasticSearch and Kibana.
-->
Fluentdがクラスタ内の各インスタンスからのログを集約されたログバックエンド(CloudWatch Logs)に送り、それらはElasticSearchとKibanaによりハイレベルなレポートに使用されます。
