---
title: "Storageclassの定義"

date: 2018-08-07T08:30:11-07:00
weight: 5
---
<!--
#### Introduction
[Dynamic Volume Provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) allows storage volumes to be created on-demand. [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/) should be pre-created to define which provisioner should be used and what parameters should be passed when dynamic provisioning is invoked.
-->
#### 紹介
[Dynamic Volume Provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)を使えば、オンデマンドでストレージボリュームを作成できます。動的なプロビジョニングが呼び出された時に必要な、provisioner情報とパラメータについて定義された[StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes/)が事前に作成されている必要があります。

<!--
#### Define Storage Class
Copy/Paste the following commands into your Cloud9 Terminal. 
-->
#### Storage Classの
Cloud9のターミナルに次のコマンドをコピー、ペーストします。
```sh
mkdir ~/environment/templates
cd ~/environment/templates
wget https://eksworkshop.com/beginner/170_statefulset/storageclass.files/mysql-storageclass.yml
```

<!--
Check the configuration of `mysql-storageclass.yml` file by following command.
-->
次のコマンドで `mysql-storageclass.yml` の内容を確認します。
```sh
cat ~/environment/templates/mysql-storageclass.yml
```

<!--
You can see the provisioner is `ebs.csi.aws.com` and type is `gp2` specified as a parameter. 
-->
provisionerは `ebs.csi.aws.com` 、タイプは `gp2` となっているのがわかります。
{{< output >}}
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: mysql-gp2
provisioner: ebs.csi.aws.com # Amazon EBS CSI driver
parameters:
  type: gp2
  encrypted: 'true' #EBS volumes will always be encrypted
reclaimPolicy: Delete
mountOptions:
- debug
{{< /output >}}

<!--
Create storageclass `mysql-gp2` by following command. 
-->
次のコマンドで `mysql-gp2` storageclassを作成します。
```sh
kubectl create -f ~/environment/templates/mysql-storageclass.yml
```

<!--
You can verify the StorageClass and its options with this command. 
-->
このコマンドでStorageClassとオプションが確認できます。
```sh
kubectl describe storageclass mysql-gp2
```
{{< output >}}
Name:                  mysql-gp2
IsDefaultClass:        No
Annotations:           <none>
Provisioner:           ebs.csi.aws.com
Parameters:            encrypted=true,type=gp2
AllowVolumeExpansion:  <unset>
MountOptions:
  debug
ReclaimPolicy:      Delete
VolumeBindingMode:  Immediate
Events:             <none>
{{< /output >}}

<!--
We will specify `mysql-gp2` as the storageClassName in volumeClaimTemplates at “Create StatefulSet” section later. 
-->
後の“StatefulSetの作成”で、volumeClaimTemplates内にstorageClassNameで `mysql-gp2` を指定します。
{{< output >}}
volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: mysql-gp2
      resources:
        requests:
          storage: 10Gi
{{< /output >}}

{{%attachments title="Related files" pattern=".yml"/%}}
