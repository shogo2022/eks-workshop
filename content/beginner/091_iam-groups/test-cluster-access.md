---
title: "EKSアクセスを試す"
date: 2020-04-05T18:00:00-00:00
draft: false
weight: 40
---

<!--
## Automate assumerole with aws cli
-->
## aws cliでassumeroleを自動化する

<!--
It is possible to automate the retrieval of temporary credentials for the assumed role by configuring the aws cli using `.aws/config`and `.aws/credentials` files.
Examples we will define 3 profile:
-->
`.aws/config`と`.aws/credentials`ファイルを編集することでaws cliを使い、引き受けたロール(assume role)の一時的なクレデンシャルの取得を自動化することができます。
3つのプロファイルを定義します:

<!--
#### add in `~/.aws/config`:

```bash
if [ ! -d ~/.aws ]; then
  mkdir ~/.aws
fi

cat << EoF >> ~/.aws/config
[profile admin]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin
source_profile=eksAdmin

[profile dev]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sDev
source_profile=eksDev

[profile integ]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg
source_profile=eksInteg

EoF
```
-->
#### `~/.aws/config`に追加:
```
cat << EoF >> ~/.aws/config
[profile admin]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin
source_profile=eksAdmin

[profile dev]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sDev
source_profile=eksDev

[profile integ]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg
source_profile=eksInteg

EoF
```

<!--
#### create `~/.aws/credentials`:

```bash
cat << EoF > ~/.aws/credentials

[eksAdmin]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PaulAdmin.json)

[eksDev]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/JeanDev.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/JeanDev.json)

[eksInteg]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PierreInteg.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PierreInteg.json)

EoF
```
-->
#### `~/.aws/credentials`の作成:
```
cat << EoF > ~/.aws/credentials

[eksAdmin]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PaulAdmin.json)

[eksDev]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/JeanDev.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/JeanDev.json)

[eksInteg]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PierreInteg.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PierreInteg.json)

EoF
```

<!--
#### Test this with dev profile:
-->
#### devプロファイルでテスト:

```bash
aws sts get-caller-identity --profile dev
```

{{<output>}}
{
    "UserId": "AROAUD5VMKW75WJEHFU4X:botocore-session-1581687024",
    "Account": "xxxxxxxxxx",
    "Arn": "arn:aws:sts::xxxxxxxxxx:assumed-role/k8sDev/botocore-session-1581687024"
}
{{</output>}}

<!--
> the assumed-role is k8sDev, so we achieved our goal
-->
> assumed-roleはk8sDevなので、成功しています

<!--
When specifying the **--profile dev** parameter we automatically ask for temporary credentials for the role k8sDev
You can test this with **integ** and **admin** also.
-->
**--profile dev**パラメータをつけると、k8sDevロールのクレデンシャルを自動的に使います。
これは**integ**と**adamin**でも試すことができます。

<details>
  <summary>with admin:</summary>
  
```bash
aws sts get-caller-identity --profile admin
{
    "UserId": "AROAUD5VMKW77KXQAL7ZX:botocore-session-1582022121",
    "Account": "xxxxxxxxxx",
    "Arn": "arn:aws:sts::xxxxxxxxxx:assumed-role/k8sAdmin/botocore-session-1582022121"
}
```

<!--
> When specifying the **--profile admin** parameter we automatically ask for temporary credentials for the role k8sAdmin
</details>
-->
**--profile admin**パラメータをつけると、k8sAdminロールのクレデンシャルを自動的に使います。
</details>

<!--
## Using AWS profiles with Kubectl config file
-->
## AWSプロファイルをKubectlコンフィグファイルと使う

<!--
It is also possible to specify the AWS_PROFILE to uses with the aws-iam-authenticator in the `.kube/config` file, so that it will uses the appropriate profile.
-->
適切なプロファイルを使うために、`.kube/config`ファイルのAWS_PROFILEをaws-iam-authnticatorと連携させることも可能です。

<!--
### with dev profile 
-->
### devプロファイル

<!--
Create new KUBECONFIG file to test this:
-->
新しいKUBECONFIGファイルの作成:

```bash
export KUBECONFIG=/tmp/kubeconfig-dev && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq w - -- 'users[*].user.exec.args[+]' '--profile' | yq w - -- 'users[*].user.exec.args[+]' 'dev' | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-dev./g' | sponge $KUBECONFIG
```

<!--
We added the `--profile dev` parameter to our kubectl config file, so that this will ask kubectl to use our IAM role associated to our dev profile, and we rename the context using suffix **-dev**.
-->
kubectlコンフィグファイルに`--profile dev`を追加し、kubectlがdevプロファイルに紐づいたIAMロールを使うようにしました。このコンテキストは、末尾に**-dev**をつけて保存されています。

<!--
With this configuration we should be able to interract with the **development** namespace, because it as our RBAC role defined.
-->
これらの設定が整い、RBACロールも定義されたので**development**名前空間にアクセスできるはずです。

<!--
let's create a pod

```bash
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx -n development
```
-->
podを作成します
```
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx -n development
```

<!--
We can list the pods
-->
podをリストします

```bash
kubectl get pods -n development
```

{{<output>}}
NAME                     READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          28h
{{</output>}}

<!--
but not in other namespaces
-->
他の名前空間ではできません

```bash
kubectl get pods -n integration
```

{{<output>}}
Error from server (Forbidden): pods is forbidden: User "dev-user" cannot list resource "pods" in API group "" in the namespace "integration"
{{</output>}}

<!--
#### Test with integ profile
-->
#### itnegプロファイルでのテスト

```bash
export KUBECONFIG=/tmp/kubeconfig-integ && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq w - -- 'users[*].user.exec.args[+]' '--profile' | yq w - -- 'users[*].user.exec.args[+]' 'integ' | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-integ./g' | sponge $KUBECONFIG
```

<!--
let's create a pod

```bash
kubectl run --generator=run-pod/v1 nginx-integ --image=nginx -n integration
```
-->
podを作りましょう
```
kubectl run --generator=run-pod/v1 nginx-integ --image=nginx -n integration
```

<!--
We can list the pods
-->
podをリストします

```bash
kubectl get pods -n integration
```

{{<output>}}
NAME          READY   STATUS    RESTARTS   AGE
nginx-integ   1/1     Running   0          43s
{{</output>}}

<!--
but not in other namespaces
-->
他の名前空間ではできません

```bash
kubectl get pods -n development
```

{{<output>}}
Error from server (Forbidden): pods is forbidden: User "integ-user" cannot list resource "pods" in API group "" in the namespace "development"
{{</output>}}

<!--
#### Test with admin profile
-->
#### adminプロファイルのテスト

```bash
export KUBECONFIG=/tmp/kubeconfig-admin && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq w - -- 'users[*].user.exec.args[+]' '--profile' | yq w - -- 'users[*].user.exec.args[+]' 'admin' | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-admin./g' | sponge $KUBECONFIG
```

<!--
let's create a pod in default namespace

```bash
kubectl run --generator=run-pod/v1 nginx-admin --image=nginx
```
-->
デフォルトの名前空間でdpodを作りましょう
```
kubectl run --generator=run-pod/v1 nginx-admin --image=nginx 
```

<!--
We can list the pods
-->
podをリストします

```bash
kubectl get pods
```

{{<output>}}
NAME          READY   STATUS    RESTARTS   AGE
nginx-integ   1/1     Running   0          43s
{{</output>}}

<!--
We can list ALL pods in all namespaces
-->
全ての名前空間の全てのpodをリストできます

```bash
kubectl get pods -A
```

{{<output>}}
NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
default       nginx-admin                1/1     Running   0          15s
development   nginx-dev                  1/1     Running   0          11m
integration   nginx-integ                1/1     Running   0          4m29s
kube-system   aws-node-mzbh4             1/1     Running   0          100m
kube-system   aws-node-p7nj7             1/1     Running   0          100m
kube-system   aws-node-v2kg9             1/1     Running   0          100m
kube-system   coredns-85bb8bb6bc-2qbx6   1/1     Running   0          105m
kube-system   coredns-85bb8bb6bc-87ndr   1/1     Running   0          105m
kube-system   kube-proxy-4n5lc           1/1     Running   0          100m
kube-system   kube-proxy-b65xm           1/1     Running   0          100m
kube-system   kube-proxy-pr7k7           1/1     Running   0          100m
{{</output>}}

<!--
## Switching between different contexts
-->
## 異なるコンテキスト間のスイッチ

<!--
It is possible to merge several kubernetes API access in the same KUBECONFIG file, or just tell Kubectl several file to lookup at once:
-->
ひとつのKUBECONFIGファイルに複数のkuubernetes APIアクセスを統合することも可能ですし、　Kubectlに複数のファイルを見に行かせることもできます:

```bash
export KUBECONFIG=/tmp/kubeconfig-dev:/tmp/kubeconfig-integ:/tmp/kubeconfig-admin
```

<!--
There is a tool [kubectx / kubens](https://github.com/ahmetb/kubectx) that will help manage KUBECONFIG files with several contexts
-->
[kubectx / kubens](https://github.com/ahmetb/kubectx)というツールは複数のコンテキストがあるKUBECONFIGファイルの管理に役立ちます

```bash
curl -sSLO https://raw.githubusercontent.com/ahmetb/kubectx/master/kubectx && chmod 755 kubectx && sudo mv kubectx /usr/local/bin
```

<!--
I can use kubectx to quickly list or swith kubernetes contexts
-->
kubectxを使うと、kubernetesコンテキストを素早くリスト、変更できます

```bash
kubectx
```

{{<output>}}
i-0397aa1339e238a99@eksworkshop-eksctl-admin.eu-west-2.eksctl.io
i-0397aa1339e238a99@eksworkshop-eksctl-dev.eu-west-2.eksctl.io
i-0397aa1339e238a99@eksworkshop-eksctl-integ.eu-west-2.eksctl.io
{{</output>}}

<!--
## Conclusion
-->
## 結論

<!--
In this module, we have seen how to configure EKS to provide finer access to users combining IAM Groups and Kubernetes RBAC.
You'll be able to create different groups depending on your needs, configure their associated RBAC access in your cluster, and simply add or remove users from the group to grand or remove them access to your cluster.
-->
このモジュールでは、IAMグループとKubernetesのRBACを組み合わせることで、ユーザのEKSへのきめ細かなアクセスを制御する方法を見ました。
必要に応じて異なるグループを作り、クラスタ内で紐づくRBACを設定し、あとはそのグループ2ユーザの追加や削除をすることでクラスタへのアクセスを制御できます。

<!--
Users will only have to configure their aws cli in order to automatically retrieve their associated rights in your cluster.
-->
ユーザは自身のaws cliを設定するだけで、自動的にクラスタ内で紐づけられた権限が取得できます。
