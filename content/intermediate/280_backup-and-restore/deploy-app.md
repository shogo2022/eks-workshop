---
title: "テストアプリのデプロイ"
weight: 30
draft: false
---

<!--
#### Create namespace and install application
-->
#### 名前空間の作成とアプリケーションのインストール

<!--
Let's create a new namespace and deploy an application in that namespace. We will deploy a Wordpress application and MySQL database backed by persistent volumes.
-->
新しい名前空間を作成し、その中にアプリケーションをデプロイしましょう。ここでは、Wordpressアプリケーションと永続ボリューム上のMySQLデータベースをデプロイします。

<!--
Create a namespace called 'staging'
-->
'staging' という名前空間を作成します
```
kubectl create namespace staging
```

{{% notice info %}}
<!--
We will be using Amazon EBS CSI Driver to manage storage for this lab. 
-->
このハンズオンでは、ストレージ管理にAmazon EBS CSIドライバを使用します。
{{% /notice %}}

<!--
Copy/Paste the following commands into your Cloud9 Terminal.
-->
Cloud9ターミナルに次のコマンドをコピー、ペーストします。
```
mkdir -p ~/environment/backup-restore
cd ~/environment/backup-restore
wget https://eksworkshop.com/intermediate/280_backup-and-restore/backupandrestore.files/storage-class.yaml
```
```
kubectl apply -f storage-class.yaml
```

<!--
#### Deploy Wordpress and MySQL in the staging namespace
-->
#### WordpressとMySQLをstaging名前空間にデプロイします

<!--
Create a kubernetes secret for MySQL DB password
-->
MySQL DBのパスワード用のsecretを作成します

```
kubectl create secret generic mysql-pass --from-literal=password=velerodemo -n staging
```

```
wget https://eksworkshop.com/intermediate/280_backup-and-restore/backupandrestore.files/mysql-deployment.yaml
wget https://eksworkshop.com/intermediate/280_backup-and-restore/backupandrestore.files/wordpress-deployment.yaml
```

<!--
Deploy MySQL and Wordpress
-->
MySQLとWordpressのデプロイ
```
kubectl apply -f mysql-deployment.yaml -n staging
kubectl apply -f wordpress-deployment.yaml -n staging
```

<!--
Verify deployment
-->
デプロイの確認
```
kubectl get deployments -n staging
```
<!--
Output should look like below:
-->
次のように表示されます:
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
wordpress         1/1     1            1           14m
wordpress-mysql   1/1     1            1           19m
```
<!--
Verify Persistent Volume Claims
-->
Persistent Volume Claimの確認
```
kubectl get pvc -n staging
```
<!--
Output should look like below:
-->
表示は次の通り:
```
NAME             STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mysql-pv-claim   Bound    pvc-babeabd3-327d-4d36-a1d9-840db1a816e1   20Gi       RWO            staging        20m
wp-pv-claim      Bound    pvc-fa71cb55-052f-484b-80c9-a55e7fb27aae   20Gi       RWO            staging        16m
```

<!--
Access Wordpress using the load balancer created by the Service.
-->
Serviceで作成されたロードバランサー経由でWordpressにアクセス
```
kubectl get svc -n staging --field-selector metadata.name=wordpress -o=jsonpath='{.items[0].status.loadBalancer.ingress[0].hostname}'
```
<!--
The output should return the load balancer's url
-->
ロードバランサーのURLが返ってくるはずです
```
a0b567aecbcf64494b68b081156ed6b5-1367665471.us-west-2.elb.amazonaws.com
```

<!--
Access the wordpress application at the load balancer url and create wordpress admin username and password.
-->
ロードバランサーのURLからwordpressにアクセスしたら、wordpressアドミンユーザとパスワードを作成します。

![Token page](/images/backupandrestore/wordpress-admin.jpg)

<!--
Create a blog post for testing and publish it
-->
blogポストを書き、公開します

![Token page](/images/backupandrestore/blogpost.jpg)

<!--
Logout and test the published blogpost
-->
ログアウトし、公開されたポストを確認します

![Token page](/images/backupandrestore/wordpress.jpg)