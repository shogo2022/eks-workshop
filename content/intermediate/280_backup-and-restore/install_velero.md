---
title: "Veleroのインストール"
weight: 20
draft: false
---

<!--
#### Install Velero binary
-->
#### Veleroバイナリのインストール

<!--
Download the [latest release's](https://github.com/vmware-tanzu/velero/releases/latest) tarball for your client platform (Example: velero-v1.3.2-linux-amd64.tar.gz)
-->
自身のクライアントプラットフォームにあった[最新リリース](https://github.com/vmware-tanzu/velero/releases/latest)のtarballをダウンロードします(例: velero-v1.3.2-linux-amd64.tar.gz)
```
wget https://github.com/vmware-tanzu/velero/releases/download/v1.3.2/velero-v1.3.2-linux-amd64.tar.gz
```
<!--
Extract the tarball:
-->
tarballを展開します:
``` 
tar -xvf velero-v1.3.2-linux-amd64.tar.gz -C /tmp
```
<!--
Move the extracted velero binary to /usr/local/bin
-->
展開したveleroバイナリを/usr/local/binに移動します
```
sudo mv /tmp/velero-v1.3.2-linux-amd64/velero /usr/local/bin
```
<!--
Verify installation
-->
インストールを確認します
```
velero version
```
<!--
Output should look something like below. We see an error getting server version because we have not installed velero on EKS cluster yet.
-->
次のような表示が出るはずです。こ時点では、EKSクラスタにVeleroがインストールされていないので、サーババージョンエラーが出ています。
```
Client:
        Version: v1.3.2
        Git commit: 55a9914a3e4719fb1578529c45430a8c11c28145
<error getting server version: the server could not find the requested resource (post serverstatusrequests.velero.io)>
```

<!--
#### Install Velero on EKS
-->
#### EKSにVeleroのインストール

<!--
Let's install velero on EKS. All the velero resources will be created in a namespace called velero.
-->
EKSにVeleroをインストールしましょう。全てのVeleroリソースはveleroという名前空間の中に作成されます。

```
velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.0.1 \
    --bucket $VELERO_BUCKET \
    --backup-location-config region=$AWS_REGION \
    --snapshot-location-config region=$AWS_REGION \
    --secret-file ./velero-credentials
```

<!--
Inspect the resources created.
-->
作成されたリソースを確認します。

```
kubectl get all -n velero
```
<!--
Output should look something like this:
-->
出力はこのようになるはずです:

```
NAME                          READY   STATUS    RESTARTS   AGE
pod/velero-845db94ffd-7c57h   1/1     Running   0          5h56m

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/velero   1/1     1            1           5h56m

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/velero-845db94ffd   1         1         1       5h56m
```