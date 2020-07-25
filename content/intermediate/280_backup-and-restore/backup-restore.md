---
title: "バックアップとリストア"
weight: 40
draft: false
---

<!--
#### Backup staging namespace using Velero
-->
#### Veleroを使ったstaging名前空間のバックアップ

<!--
Let's backup the staging namespace using velero
-->
veleroを使ってstaging名前空間をバックアップしましょう

```
velero backup create staging-backup --include-namespaces staging
```

<!--
Check the status of backup
-->
バックアップのステータス確認
```
velero backup describe staging-backup
```

<!--
The output should look like below. Check if the Phase is Completed and if the snapshots are created.
-->
次のように表示されるはずです。PhaseがCompletedになっていることと、スナップショットが作成されていることを確認してください。
```
Name:         staging-backup
Namespace:    velero
Labels:       velero.io/storage-location=default
Annotations:  <none>

Phase:  Completed

Namespaces:
  Included:  staging
  Excluded:  <none>

Resources:
  Included:        *
  Excluded:        <none>
  Cluster-scoped:  auto

Label selector:  <none>

Storage Location:  default

Snapshot PVs:  auto

TTL:  720h0m0s

Hooks:  <none>

Backup Format Version:  1

Started:    2020-04-16 02:25:53 +0000 UTC
Completed:  2020-04-16 02:26:08 +0000 UTC

Expiration:  2020-05-16 02:25:53 +0000 UTC

Persistent Volumes:  2 of 2 snapshots completed successfully (specify --details for more information)
```

<!--
Access Velero S3 bucket using AWS Managment Console and verify if 'staging-backup' have been created.
-->
AWS管理コンソールからVeleroのS3バケットにアクセスし、'staging-backup'が作成されていることを確認します。
![Title Image](/images/backupandrestore/velero-bucket.jpg)

<!--
#### Simulate a disaster
-->
#### 災害シミュレーション

<!--
Let's delelte the 'staging' namespace to simulate a disaster
-->
災害を想定して、'staging'の名前空間を削除します
```
kubectl delete namespace staging
```

<!--
Verify that MySQL and Wordpress are deleted. The command below should return *No resources found.*
-->
MySQLとWordpressが削除されたことを確認します。次のコマンドで *No resources found.* が返ってくるはずです。
```
kubectl get all -n staging
```

<!--
#### Restore staging namespace
-->
#### staging名前空間のリストア

<!--
Run the velero restore command from the backup created. It may take a couple of minutes to restore the namespace. 
-->
作成済みのバックアップからvelero restoreコマンドを実行します。名前空間をリストアするのには数分かかります。
```
velero restore create --from-backup staging-backup
```
<!--
You can check the restore status using the command below:
-->
次のコマンドでリストア状況を確認できます:
```
velero restore get
```
<!--
Check restore STATUS in the output.
-->
出力のステータス(STATUS)欄を確認します。
```
NAME                            BACKUP           STATUS      WARNINGS   ERRORS   CREATED                         SELECTOR
staging-backup-20200416024049   staging-backup   Completed   0          0        2020-04-16 02:40:50 +0000 UTC   <none>
```

<!--
Verify if deployments, replicasets, services and pods are restored
-->
deployment、replicaset、serviceそしてpodがリストアされたことを確認します
```
kubectl get all -n staging
```
<!--
Output will look something like below:
-->
次のように出力されます:
```
NAME                                  READY   STATUS    RESTARTS   AGE
pod/wordpress-549c4f6867-r9wl9        1/1     Running   0          8m37s
pod/wordpress-mysql-67565bd57-rx2c5   1/1     Running   0          8m37s


NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP                                                              PORT(S)        AGE
service/wordpress         LoadBalancer   10.100.139.5   af09f28402adc47af8c7f667f439ed51-334979785.us-west-2.elb.amazonaws.com   80:31439/TCP   8m36s
service/wordpress-mysql   ClusterIP      None           <none>                                                                   3306/TCP       8m37s


NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/wordpress         1/1     1            1           8m37s
deployment.apps/wordpress-mysql   1/1     1            1           8m37s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/wordpress-549c4f6867        1         1         1       8m37s
replicaset.apps/wordpress-mysql-67565bd57   1         1         1       8m37s

```

<!--
Access Wordpress using the load balancer created by the Service.
-->
Serviceが作成したロードバランサー経由でWordpressにアクセスします。
```
kubectl get svc -n staging --field-selector metadata.name=wordpress -o=jsonpath='{.items[0].status.loadBalancer.ingress[0].hostname}'
```
<!--
The output should return the load balancer's url
-->
ロードバランサーのURLが返ってきます
```
af09f28402adc47af8c7f667f439ed51-334979785.us-west-2.elb.amazonaws.com
```

<!--
Access the wordpress application at load balancer's url and verify if the blog post you created is restored.
-->
ロードバランサーのURLでWordpressにアクセスし、作成したポストがリストアされエイルことを確認します。