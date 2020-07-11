---
title: "IAMユーザの作成"
date: 2018-10-03T10:14:46-07:00
draft: false
weight: 22
---

<!--
In order to test our scenarios, we will create 3 users, one for each groups we created :
-->
シナリオを試すためには、作成したそれぞれのグループに一つずつ、3つのユーザを作成します:

```bash
aws iam create-user --user-name PaulAdmin
aws iam create-user --user-name JeanDev
aws iam create-user --user-name PierreInteg
```

<!--
Add users to associated groups:

```bash
aws iam add-user-to-group --group-name k8sAdmin --user-name PaulAdmin
aws iam add-user-to-group --group-name k8sDev --user-name JeanDev
aws iam add-user-to-group --group-name k8sInteg --user-name PierreInteg
```
-->
ユーザのグループへの割り当て:
```
aws iam add-user-to-group --group-name k8sAdmin --user-name PaulAdmin
aws iam add-user-to-group --group-name k8sDev --user-name JeanDev
aws iam add-user-to-group --group-name k8sInteg --user-name PierreInteg
```

<!--
Check users are correctly added in their groups:

```bash
aws iam get-group --group-name k8sAdmin
aws iam get-group --group-name k8sDev
aws iam get-group --group-name k8sInteg
```
-->
ユーザがグループに追加されたかの確認:
```
aws iam get-group --group-name k8sAdmin
aws iam get-group --group-name k8sDev
aws iam get-group --group-name k8sInteg
```

<!--
{{% notice note %}}
For the sake of simplicity, in this chapter, we will save credentials to a file to make it easy to toggle back and forth between users.  Never do this in production or with credentials that have priviledged access; It is not a security best practice to store credentials on the filesystem.
{{% /notice %}}
-->
{{% notice note %}}
このチャプターでは、ユーザの切替をシンプルにするためにクレデンシャルをファイルに保存します。商用環境、または権限が大きいユーザでは決してやってはいけません; クレデンシャルをファイルに保存するのはセキュリティのベストプラクティスではありません。
{{% /notice %}}

<!--
Retrieve Access Keys for our fake users:
-->
仮ユーザのアクセスキーを取得します:

```bash
aws iam create-access-key --user-name PaulAdmin | tee /tmp/PaulAdmin.json
aws iam create-access-key --user-name JeanDev | tee /tmp/JeanDev.json
aws iam create-access-key --user-name PierreInteg | tee /tmp/PierreInteg.json
```

<!-- TODO: remove this
```
cat << EoF > ~/.aws/PaulAdmin_creds.sh
export AWS_SECRET_ACCESS_KEY=$(jq .AccessKey.SecretAccessKey /tmp/PaulAdmin.json)
export AWS_ACCESS_KEY_ID=$(jq .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
EoF
cat << EoF > ~/.aws/JeanDev_creds.sh
export AWS_SECRET_ACCESS_KEY=$(jq .AccessKey.SecretAccessKey /tmp/JeanDev.json)
export AWS_ACCESS_KEY_ID=$(jq .AccessKey.AccessKeyId /tmp/JeanDev.json)
EoF
cat << EoF > ~/.aws/PierreInteg_creds.sh
export AWS_SECRET_ACCESS_KEY=$(jq .AccessKey.SecretAccessKey /tmp/PierreInteg.json)
export AWS_ACCESS_KEY_ID=$(jq .AccessKey.AccessKeyId /tmp/PierreInteg.json)
EoF
```
-->

<!--
Recap: 
-->
まとめ:

<!--
- **PaulAdmin** is in the **k8sAdmin** group and will be able to assume the **k8sAdmin** role.
- **JeanDev** is in **k8sDev** Group and will be able to assume IAM role **k8sDev**
- **PierreInteg** is in **k8sInteg** group and will be able to assume IAM role **k8sInteg**
-->
- **PaulAdmin** は **k8sAdmin** グループに属し、 **k8sAdmin** ロールを引き受けることができる。
- **JeanDev** は **k8sDev** グループに属し、 **k8sDev** IAMロールを引き受けることができる。
- **PierreInteg** は **k8sInteg** グループに属し、 **k8sInteg** IAMロールを引き受けることができる。
