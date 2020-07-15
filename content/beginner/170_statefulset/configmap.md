---
title: "ConfigMapの作成"
date: 2018-08-07T08:30:11-07:00
weight: 10
---

<!--
#### Introduction
[ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) allow you to decouple configuration artifacts and secrets from image content to keep containerized applications portable. Using ConfigMap, you can independently control MySQL configuration. 
-->
#### 紹介
[ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)は、設定ファイルやsecretとイメージを分離し、コンテナ化されたアプリケーションの携帯性を高めます。ConfigMapを使うことで、MySQLの設定を個別に管理することができます。

<!--
#### Create the mysql Namespace
We will create a new `Namespace` called `mysql` that will host all the components.
-->
#### mysql名前空間の作成
新しく `mysql` という `名前空間` を作成します。
```sh
kubectl create namespace mysql
```

<!--
#### Create ConfigMap
Run the following commands to download the `ConfigMap`.
-->
#### ConfigMapの作成
次のコマンドを実行し、 `ConfigMap` をダウンロードします。
```sh
cd ~/environment/templates
wget https://eksworkshop.com/beginner/170_statefulset/configmap.files/mysql-configmap.yml

```

<!--
Check the configuration of mysql-configmap.yml file.
-->
mysql-configmap.yamlファイルの設定を確認します。
```sh
cat ~/environment/templates/mysql-configmap.yml
```

<!--
The `ConfigMap` stores master.cnf, slave.cnf and passes them when initializing leader and follower pods defined in StatefulSet:
* **master.cnf** is for the MySQL leader pod which has binary log option (log-bin) to provides a record of the data changes to be sent to follower servers.
* **slave.cnf** is for follower pods which have super-read-only option.
-->
`ConfigMap` にはmaster.cnfとslave.cnfが入っており、StatefulSetdで定義されたリーダーとフォロワーが初期化される時に渡されます:
* **master.cnf** は、フォロワーサーバにデータ変更のレコードを渡すためのバイナリログオプションがある、MySQLリーダーpodに使われます。
* **slave.cnf** は、super-read-onlyオプションがあるフォロワーpodに使われます。
{{< output >}}
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-config
  namespace: mysql
  labels:
    app: mysql
data:
  master.cnf: |
    # Apply this config only on the leader.
    [mysqld]
    log-bin
  slave.cnf: |
    # Apply this config only on follower.
    [mysqld]
    super-read-only
{{< /output >}}

<!--
Create "mysql-config" `ConfigMap`.
-->
"mysql-config" `ConfigMap` を左悪性します。
```sh
kubectl create -f ~/environment/templates/mysql-configmap.yml
```

{{%attachments title="Related files" pattern=".yml"/%}}
