---
title: "はじめに"
chapter: false
weight: 1
---

<!--
After you've completed the prerequisites and Helm is installed and working; We can deploy our Wordpress site.  This Helm chart will deploy MariaDB and Wordpress as well as configure a service ingress point for us to access the site through an elastic load balancer.
-->
前提条件を満たし、Helmがインストールされたら、Wordpressサイトをデプロイします。このHelmチャートはMariaDBとWordpressをデプロイし、elastic load balancerを通じてアクセスをさせるためのingressサービスも設定します。

<!--
For our testing we’ll be deploying Wordpress. We could just use a PHP file on the nodes and run NGINX to test as well, but with this Wordpress install you get experience deploying a Helm chart. And can use the load testing tool to hit various URLs on the Wordpress structure to generate additional network traffic load with multiple concurrent connections.
-->
ここではテスト用にWordpressをデプロイします。PHPファイルとNGINXでテストをすることもできますが、Helmチャートを経験するためにWordpressをインストールします。また、負荷テストツールを使うことで、Wordpressの様々なURLをたたき、複数の同時接続を通じてネットワークの負荷を再現することができます。

<!--
#### We'll be using the following tools in this lab:
-->
#### このハンズオンでは次のツールを使います:

<!--
- **Helm**: to install Wordpress on our cluster.
- **CloudWatch Container Insights**: to collect logs and metrics from our cluster.
- **Siege**: to load test our Wordpress and EKS Cluster.
- **CloudWatch Container Insights Dashboard**: to visualize our container performance and load.
- **CloudWatch Metrics**: to set an alarm for when our WordPress Pod is under heavy load.
-->
- **Helm**: クラスタにWordpressをインストール
- **CloudWatch Container Insights**: クラスタからログとメトリクスの収集
- **Siege**: WordpressとEKSクラスタへのお負荷試験
- **CloudWatch Container Insights Dashboard**: コンテナのおパフォーマンスと負荷の視覚化
- **CloudWatch Metrics**: Wordpress podが強い負荷を受けた時のアラーム設定

<!--
### Lets get started!
-->
### 始めましょう!