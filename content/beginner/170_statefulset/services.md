---
title: "Serviceの作成"
date: 2018-08-07T08:30:11-07:00
weight: 15
---
<!--
#### Introduction
[Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/) defines a logical set of Pods and a policy by which to access them. Service can be exposed in different ways by specifying a [type](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/) in the serviceSpec. StatefulSet currently requires a [Headless Service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services) to control the domain of its Pods, directly reach each Pod with stable DNS entries. By specifying **"None"** for the clusterIP, you can create Headless Service.
#### Create Services
Copy/Paste the following commands into your Cloud9 Terminal.
-->
#### 紹介
[Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/)はpodの論理的な集まりとそれらにどのようにアクセスさせるかを定義したものです。ServiceはserviceSpecの中の[type](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)を使って異なる方法で公開することができます。StatefulSetは現在、[Headless Service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services)を必要としており、DNS名でPodに直接疎通をとるためにドメイン制御をしています。 **"None"** をclusterIPに指定することで、Headless Serviceを作成できます。
#### Serviceの作成
Cloud9のターミナルに以下のコマンドをコピー、ペーストしてください。

```
cd ~/environment/templates
wget https://eksworkshop.com/beginner/170_statefulset/services.files/mysql-services.yml
```
<!--
Check the configuration of mysql-services.yml by following command.
-->
次のコマンドでmysql-services.ymlの設定を確認します。
```
cat ~/environment/templates/mysql-services.yml
```
<!--
You can see the **mysql** service is for DNS resolution so that when pods are placed by StatefulSet controller, pods can be resolved using pod-name.mysql. **mysql-read** is a client service that does load balancing for all followers. 
-->
DNS解決のための **mysql** serviceがあるので、StatefulSet controllerがpodを配置すると、podがpod-name.mysqlで名前解決できるようになっています。 **mysql-read** は全てのフォロワーにロードバランスするためのクライアントサービスです。
<!--
{{< output >}}
# Headless service for stable DNS entries of StatefulSet members.
apiVersion: v1
kind: Service
metadata:
  namespace: mysql
  name: mysql
  labels:
    app: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  clusterIP: None
  selector:
    app: mysql
---
# Client service for connecting to any MySQL instance for reads.
# For writes, you must instead connect to the leader: mysql-0.mysql.
apiVersion: v1
kind: Service
metadata:
  namespace: mysql
  name: mysql-read
  labels:
    app: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  selector:
    app: mysql
{{< /output >}}
-->
{{< output >}}
# StatefulSetメンバーのDNSエントリをつくるためのHeadless service
apiVersion: v1
kind: Service
metadata:
  namespace: mysql
  name: mysql
  labels:
    app: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  clusterIP: None
  selector:
    app: mysql
---
# 読み取りのためのMySQLインスタンスに接続するためのクライアントサービス
# 書き込みは、リーダーであるmysql-0.mysqlに接続が必要
apiVersion: v1
kind: Service
metadata:
  namespace: mysql
  name: mysql-read
  labels:
    app: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  selector:
    app: mysql
{{< /output >}}
<!--
Create service mysql and mysql-read by following command
-->
次のコマンドでmysqlとmysql-read serviceを作成する
```
kubectl create -f ~/environment/templates/mysql-services.yml
```
{{%attachments title="Related files" pattern=".yml"/%}}
