---
title: "WordPressのインストール"
chapter: false
weight: 2
---

![alt text](/images/ekscwci/wordpresslogo.png "Wordpress Logo")

<!--
{{% notice note %}}
We'll be using the [bitnami charts repository](https://github.com/bitnami/charts) to install WordPress to our EKS cluster.
{{% /notice %}}
-->
{{% notice note %}}
EKSクラスタにWordPressをインストールするのには[bitnami charts repository](https://github.com/bitnami/charts)を使います。
{{% /notice %}}

<!--
In your Cloud9 Workspace terminal you just need to run the following commands to deploy WordPress and its database.
-->
Cloud9のワークスペースターミナルであれば、次のコマンドを実行するだけでWOrdPressとデータベースをデプロイできます。

<!--
```bash
# Create a namespace wordpress
kubectl create namespace wordpress-cwi

# Add the bitnami Helm Charts Repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Deploy WordPress in its own namespace
helm -n wordpress-cwi install understood-zebu bitnami/wordpress
```
-->
```bash
# wordpress用の名前空間を作成します
kubectl create namespace wordpress-cwi

# bitnamiのHelmチャートレポジトリを追加します
helm repo add bitnami https://charts.bitnami.com/bitnami

# WordPressを名前空間にデプロイします
helm -n wordpress-cwi install understood-zebu bitnami/wordpress
```

<!--
This chart will create:
-->
このチャートは次のものを作ります:

<!--
* Two [persistent volumes claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)..
* Multiple [secrets](https://kubernetes.io/docs/concepts/configuration/secret/).
* One [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) for MariaDB.
* One [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) for Wordpress.
-->
* 2つの[persistent volumes claims](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)..
* 複数の[secrets](https://kubernetes.io/docs/concepts/configuration/secret/).
* 1つの[StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) for MariaDB.
* １つの[Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) for Wordpress.

<!--
You can follow the status of the deployment with this command
-->
このコマンドでdeploymentの状況を確認できます

```bash
kubectl -n wordpress-cwi rollout status deployment understood-zebu-wordpress
```
