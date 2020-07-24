---
title: "ダッシュボード"
date: 2018-10-14T21:03:43-04:00
weight: 20
draft: false
---

<!--
## Log in to Grafana
-->
## Grafanaにログイン

<!--
Log in to Grafana dashboard using credentials supplied during configuration.
-->
設定の際に使ったクレデンシャルでGrafanaダッシュボードにログインします。

<!--
You will notice that **'Install Grafana'** & **'create your first data source'** are already completed. We will import community created dashboard for this tutorial.
-->
**'Install Grafana'** と **'create your first data source'** が既に完了していることが確認できます。このチュートリアルではコミュニティで提供されているダッシュボードをインポートします。

<!--
### Cluster Monitoring Dashboard
-->
### クラスタ監視ダッシュボード

<!--
For creating a dashboard to monitor the cluster:
-->
クラスタを監視するダッシュボードを作成します:

<!--
* Click **'+'** button on left panel and select **'Import'**.
* Enter **3119** dashboard id under Grafana.com Dashboard.
* Click **'Load'**.
* Select **'Prometheus'** as the endpoint under prometheus data sources drop down.
* Click **'Import'**.
-->
* 左のパネルにある **'+'** ボタンをクリックし **'Import'** を選択
* Grafana.com Dashboardの下のdashboard idに **3119** を入力
* **'Load'** をクリック
* prometheusデータソースのドロップダウンから **'Prometheus'** をエンドポイントに選択
* **'Import'** をクリック

<!--
This will show monitoring dashboard for all cluster nodes
-->
これはあ全てのクラスタノードの監視ダッシュボードを表示します

![grafana-all-nodes](/images/grafana-all-nodes.png)

<!--
### Pods Monitoring Dashboard
-->
### Pod監視ダッシュボード

<!--
For creating a dashboard to monitor all the pods:
-->
全てのpodを監視するダッシュボードを作成するには次を実行します:

<!--
* Click **'+'** button on left panel and select **'Import'**.
* Enter **6417** dashboard id under Grafana.com Dashboard.
* Click **'Load'**.
* Enter **Kubernetes Pods Monitoring** as the Dashboard name.
* Click **change** to set the Unique identifier (uid).
* Select **'Prometheus'** as the endpoint under prometheus data sources drop down.s
* Click **'Import'**.
-->
* 左のパネルにある **'+'** ボタンをクリックし **'Import'** を選択
* Grafana.com Dashboardの下のdashboard idに **6417** を入力
* **'Load'** をクリック
* ダッシュボードの名前に **Kubernetes Pods Monitoring** と入力
* **change** をクリックし、任意のIDを入力
* prometheusデータソースのドロップダウンから **'Prometheus'** をエンドポイントに選択
* **'Import'** をクリック

![grafana-all-pods](/images/grafana-all-pods.png)
