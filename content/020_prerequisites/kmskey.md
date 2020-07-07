---
title: "AWS KMS Custom Managed Key (CMK)の作成"
chapter: false
weight: 32
---


<!--
Create a CMK for the EKS cluster to use when encrypting your Kubernetes secrets:
```bash
aws kms create-alias --alias-name alias/eksworkshop --target-key-id $(aws kms create-key --query KeyMetadata.Arn --output text)
```
-->
Kubernetesクラスターのsecretsを暗号化するためのCMKを作成します:
```bash
aws kms create-alias --alias-name alias/eksworkshop --target-key-id $(aws kms create-key --query KeyMetadata.Arn --output text)
```

<!--
Let's retrieve the ARN of the CMK to input into the create cluster command.
-->
create clusterコマンドに追加するためにCMKのARNを取得しましょう。

```bash
export MASTER_ARN=$(aws kms describe-key --key-id alias/eksworkshop --query KeyMetadata.Arn --output text)
```

<!--
We set the MASTER_ARN environment variable to make it easier to refer to the KMS key later.
-->
あとでKMSキーを参照しやすいようにMASER_ARN環境変数にセットしています。

<!--
Now, let's save the MASTER_ARN environment variable into the bash_profile
-->
では、MASTER_ARN環境変数をbash_profileに保存しましょう

```bash
echo "export MASTER_ARN=${MASTER_ARN}" | tee -a ~/.bash_profile
```
