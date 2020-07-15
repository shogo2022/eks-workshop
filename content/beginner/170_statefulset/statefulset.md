---
title: "StatefulSetの作成"
date: 2018-08-07T08:30:11-07:00
weight: 20
---
<!--
#### Introduction
StatefulSet consists of serviceName, replicas, template and volumeClaimTemplates:
* **serviceName** is "mysql", headless service we created in previous section
* **replicas** is 3, the desired number of pod
* **template** is the configuration of pod
* **volumeClaimTemplates** is to claim volume for pod based on storageClassName, `mysql-gp2` that we created in the [Define Storageclass](/beginner/170_statefulset/storageclass/) section.
-->
#### 紹介
StatefulSetはserviceName、replicas、templateそしてvolumeClaimTemplatesで構成されています:
* **serviceName** は"mysql"で、これは前のセクションで私たちが作成したheadless serviceです
* **replicas** は3で、これはpodの数を記載します
* **template** はpodの設定です
* **volumeClaimTemplates** は[Storageclassの定義](/beginner/170_statefulset/storageclass/)セクションで作成した、 `mysql-gp2` をstorageClassNameとして指定し、これに基づいてpodがボリュームを申告します。

<!--
{{% notice info %}}
[Percona Xtrabackup](https://www.percona.com/software/mysql-database/percona-xtrabackup) is used in the template to clone source MySQL server to its followers.
{{% /notice %}}
-->
{{% notice info %}}
ここではMySQLサーバをフォロワにクローンをするために[Percona Xtrabackup](https://www.percona.com/software/mysql-database/percona-xtrabackup)がテンプレートで使われています。
{{% /notice %}}

<!--
#### Create StatefulSet
Copy/Paste the following commands into your Cloud9 Terminal.
-->
#### StatefulSetの作製
Cloud9のターミナルに次のコマンドをコピー、ペーストします。
```
cd ~/environment/templates
wget https://eksworkshop.com/beginner/170_statefulset/statefulset.files/mysql-statefulset.yml
```
<!--
Create the `StatefulSet` "mysql" by following command.
-->
次のコマンドで"mysql"という `StatefulSet` を作成します。
```
kubectl create -f ~/environment/templates/mysql-statefulset.yml
```
<!--
#### Watch StatefulSet
Watch the status of `StatefulSet`.
-->
#### StatefulSetの状態確認
```sh
kubectl -n mysql rollout status statefulset mysql
```

<!--
It will take few minutes for pods to initialize and have `StatefulSet` created.
-->
podが初期化され、 `StatefulSet` が作成されるまでには数分かかります。
{{< output >}}
Waiting for 3 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 1 pods to be ready...
partitioned roll out complete: 3 new pods have been updated...
{{< /output >}}

<!--
Open another Cloud9 Terminal and watch the progress of pods creation using the following command. 
-->
別のCloud9ターミナルを開き、次のコマンドでpodの作成状況をwatchします。
```sh
kubectl -n mysql get pods -l app=mysql --watch
```

<!--
You can see ordered, graceful deployment with a stable, unique name for each pod.
-->
それぞれに一意の名前がついたpodが順番にデプロイされていくのが見えます。
{{< output >}}
NAME      READY     STATUS     RESTARTS   AGE
mysql-0   0/2       Init:0/2   0          30s
mysql-0   0/2       Init:1/2   0         35s
mysql-0   0/2       PodInitializing   0         47s
mysql-0   1/2       Running   0         48s
mysql-0   2/2       Running   0         59s
mysql-1   0/2       Pending   0         0s
mysql-1   0/2       Pending   0         0s
mysql-1   0/2       Pending   0         0s
mysql-1   0/2       Init:0/2   0         0s
mysql-1   0/2       Init:1/2   0         35s
mysql-1   0/2       Init:1/2   0         45s
mysql-1   0/2       PodInitializing   0         54s
mysql-1   1/2       Running   0         55s
mysql-1   2/2       Running   0         1m
mysql-2   0/2       Pending   0         0s
mysql-2   0/2       Pending   0         0s
mysql-2   0/2       Pending   0         0s
mysql-2   0/2       Init:0/2   0         0s
mysql-2   0/2       Init:1/2   0         32s
mysql-2   0/2       Init:1/2   0         43s
mysql-2   0/2       PodInitializing   0         50s
mysql-2   1/2       Running   0         52s
mysql-2   2/2       Running   0         56s
{{< /output >}}

<!--
{{% notice info %}}
Press Ctrl+C to stop watching.
{{% /notice %}}
-->
{{% notice info %}}
Ctrl+Cを押してwatchを止めます。
{{% /notice %}}


<!--
Check the dynamically created PVC by following command.
-->
次のコマンドで動的に作られたPVCを確認します。
```sh
kubectl -n mysql get pvc -l app=mysql
```

<!--
We can see `data-mysql-0`,`data-mysql-1`, and `data-mysql-2` have been created with the STORAGECLASS `mysql-gp2`.
-->
`data-mysql-0` 、 `data-mysql-1` 、そして `data-mysql-2` がSTORAGECLASS `mysql-gp2` で作成されたことがわかります。
{{< output >}}
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-mysql-0   Bound    pvc-2a9bb222-3fbe-11ea-94be-0aff3e98c5a0   10Gi       RWO            mysql-gp2      22m
data-mysql-1   Bound    pvc-47076f1d-3fbe-11ea-94be-0aff3e98c5a0   10Gi       RWO            mysql-gp2      21m
data-mysql-2   Bound    pvc-6b3d6667-3fbe-11ea-94be-0aff3e98c5a0   10Gi       RWO            mysql-gp2      20m
{{< /output >}}

<!--
And now the same information from the EC2 console.
-->
EC2コンソールからも同じ情報が確認できます。

<!--
{{% notice tip %}}
We can see the EBS volumes have been automatically encrypted by the [AWS Key Management Service (KMS)](https://aws.amazon.com/kms/)
{{% /notice %}}
-->
{{% notice tip %}}
EBSボリュームが自動的に[AWS Key Management Service (KMS)](https://aws.amazon.com/kms/)によって確認されたことが確認できます。
{{% /notice %}}

![EBS_volume](/images/statefulset/ebs_volume.png)
