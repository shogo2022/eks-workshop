---
title: "Elasticsearch、Fluentd、Kibana (EFK)でログ取得"
chapter: true
weight: 230
tags:
  - advanced
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
* [Fluentd](https://www.fluentd.org/) is an open source data collector providing a unified logging layer, supported by 500+ plugins connecting to many types of systems.
* [Elasticsearch](https://www.elastic.co/products/elasticsearch) is a distributed, RESTful search and analytics engine.
* [Kibana](https://www.elastic.co/products/kibana) lets you visualize your Elasticsearch data.
-->
* [Fluentd](https://www.fluentd.org/) は、多くのシステムに繋がる500以上のプラグインで統一されたロギング機能を提供する、オープンソースのデータコレクターです。
* [Elasticsearch](https://www.elastic.co/products/elasticsearch) は分散環境で動く、RESTfulな検索及び分析エンジンです。
* [Kibana](https://www.elastic.co/products/kibana) はElasticsearchのデータを可視化します。

<!--
Together, Fluentd, Elasticsearch and Kibana is also known as “EFK stack”. Fluentd will forward logs from the individual instances in the cluster to a centralized logging backend (CloudWatch Logs) where they are combined for higher-level reporting using ElasticSearch and Kibana.
-->
Fluentd、Elasaticsearch、そしてKibanaをまとめて、"EFKスタック"としても知られています。Fluentdがクラスタ内の各インスタンスからのログを集約されたログバックエンド(CloudWatch Logs)に送り、それらはElasticSearchとKibanaによりハイレベルなレポートに使用されます。