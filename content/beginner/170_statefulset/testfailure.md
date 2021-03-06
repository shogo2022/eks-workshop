---
title: "障害テスト"
date: 2018-08-07T08:30:11-07:00
weight: 30
---
<!--
#### Unhealthy container
MySQL container uses readiness probe by running mysql -h 127.0.0.1 -e 'SELECT 1' on the server to make sure MySQL server is still active.
Open a new terminal and simulate MySQL as being unresponsive by following command.
-->
#### 正常でないコンテナ
MySQLコンテナはmysql -h 127.0.0.1 -e 'SELECT 1'をサーバで実行することでレディネスプローブとし、MySQLサーバが稼働しているかを保証しています。
新しいターミナルを開き、次のコマンドを実行しMySQLが反応しなくなった状態を再現します。
```
kubectl -n mysql exec mysql-2 -c mysql -- mv /usr/bin/mysql /usr/bin/mysql.off
```

<!--
This command renames the /usr/bin/mysql command so that readiness probe can't find it. During the next health check, the pod should report one of it's containers is not healthy. This can be verified by following command.
-->
このコマンドは/usr/bin/mysqlをリネームし、レディネスプローブが使えないようにしてしまいます。次のヘルスチェックで、コンテナの内の一つが正常でないと報告されるはずです。次のコマンドで確認します。
```sh
kubectl -n mysql get pod mysql-2
```

{{< output >}}
NAME      READY     STATUS    RESTARTS   AGE
mysql-2   1/2       Running   0          12m
{{< /output >}}

<!--
**mysql-read** load balancer detects failures and takes action by not sending traffic to the failed container, `@@server_id 102`. You can check this by the loop running in the separate window from previous section. The loop shows the following output.
-->
**mysql-read** ロードバランサーは障害を発見し、障害のある `@@server_id 102` コンテナには通信を流さないようにします。これは前のセクションで流したままにしておいたループで確認ができます。表示は次のよおうになっています。
{{< output >}}
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2020-01-25 17:32:19 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         100 | 2020-01-25 17:32:20 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         101 | 2020-01-25 17:32:21 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         100 | 2020-01-25 17:32:22 |
+-------------+---------------------+
+-------------+---------------------+
| @@server_id | NOW()               |
+-------------+---------------------+
|         100 | 2020-01-25 17:32:23 |
+-------------+---------------------+
{{< /output >}}

<!--
Revert back to its initial state at the previous terminal.
-->
前のターミナルに戻り、障害を元に戻します。
```sh
kubectl -n mysql exec mysql-2 -c mysql -- mv /usr/bin/mysql.off /usr/bin/mysql
```

<!--
Check the status again to see that both containers are running and healthy
-->
ステータスを再度確認し、コンテナが両方共正常に稼働していることを確認します
```sh
kubectl -n mysql get pod mysql-2
```
{{< output >}}
NAME      READY     STATUS    RESTARTS   AGE
mysql-2   2/2       Running   0          5h
{{< /output >}}
<!--
The loop in another terminal is now showing `@@server_id 102` is back and all three servers are running.
Press Ctrl+C to stop watching.
#### Failed pod
To simulate a failed pod, delete mysql-2 pod by following command.
-->
もう一つのターミナル上のループに `@@server_id 102` が戻り、3つ全てのサーバが稼働していることが確認できます。
Ctrl+Cを押して、watchを止めてください。
#### 障害のあるpod
podの障害を再現するには、次のコマンドでmysql-2 podを削除します。
```sh
kubectl -n mysql delete pod mysql-2
```
{{< output >}}
pod "mysql-2" deleted
{{< /output >}}

<!--
StatefulSet controller recognizes failed pod and creates a new one to maintain the number of replicas with the same name and link to the same `PersistentVolumeClaim`.
-->
StatefulSet controllerはpodの障害を検知し、レプリカの数を保つために新しいpodを同じ名前、同じ `PersistentVolumeClaim` で作成します。
```sh
kubectl -n mysql get pod mysql-2 -w
```

{{< output >}}
NAME      READY   STATUS        RESTARTS   AGE
mysql-2   2/2     Terminating   0          15m
mysql-2   0/2     Terminating   0          16m
mysql-2   0/2     Terminating   0          16m
mysql-2   0/2     Terminating   0          16m
mysql-2   0/2     Pending       0          0s
mysql-2   0/2     Pending       0          0s
mysql-2   0/2     Init:0/2      0          0s
mysql-2   0/2     Init:1/2      0          11s
mysql-2   0/2     PodInitializing   0          12s
mysql-2   1/2     Running           0          13s
mysql-2   2/2     Running           0          18s
{{< /output >}}

<!--
{{% notice tip %}}
Press Ctrl+C to stop watching.
{{% /notice %}}
-->
{{% notice tip %}}
Ctrl+Cを押してwatchを止めます。
{{% /notice %}}