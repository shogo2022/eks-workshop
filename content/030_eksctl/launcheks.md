---
title: "EKSの起動"
date: 2018-08-07T13:34:24-07:00
weight: 20
---


<!--
{{% notice warning %}}
**DO NOT PROCEED** with this step unless you have [validated the IAM role](/020_prerequisites/workspaceiam/#validate-the-iam-role) in use by the Cloud9 IDE. You will not be able to run the necessary kubectl commands in the later modules unless the EKS cluster is built using the IAM role.
{{% /notice %}}
-->
{{% notice warning %}}
Cloud9のIDEで使用されている[IAMロールを確認する](/020_prerequisites/workspaceiam/#validate-the-iam-role)までは**進まないでください**。IAMロールを使用してEKSクラスターが作成されていない場合、後のモジュールで必要なkubectlコマンドが使用できません。
{{% /notice %}}

<!--
#### Challenge:
-->
#### チャレンジ:

<!--
**How do I check the IAM role on the workspace?**
-->
**ワークスペースのIAMロールの確認方法は?**

<!--
{{%expand "Expand here to see the solution" %}}
Run `aws sts get-caller-identity` and validate that your _Arn_ contains `eksworkshop-admin`and an Instance Id.

```output
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/eksworkshop-admin/i-01234567890abcdef"
}
```

If you do not see the correct role, please go back and [validate the IAM role](/020_prerequisites/workspaceiam/#validate-the-iam-role) for troubleshooting.

If you do see the correct role, proceed to next step to create an EKS cluster.
{{% /expand %}}
-->
{{%expand "答えはこちら" %}}
`aws sts get-caller-identity`を実行して、_Arn_に`eksworkshop-admin`とインスタンスIDが含まれていることを確認します。

```output
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/eksworkshop-admin/i-01234567890abcdef"
}
```

正しいロールでない場合は、戻って[IAMロールを確認](/020_prerequisites/workspaceiam/#validate-the-iam-role)して調査してください。

正しいロールの場合は、次のステップに進みEKSクラスターを作成します。
{{% /expand %}}

<!--
### Create an EKS cluster
-->
### EKSクラスターの作成

<!--
{{% notice warning %}}
`eksclt` version must be 0.24.0 or above to deploy EKS 1.17, [click here](/030_eksctl/prerequisites) to get the latest version.
{{% /notice %}}
-->
{{% notice warning %}}
EKS 1.17をデプロイするには、 `eksclt` のバージョンが0.24.0以上である必要があります、[ここをクリック](/030_eksctl/prerequisites)して最新版をデプロイしてください。
{{% /notice %}}

<!--
Create an eksctl deployment file (eksworkshop.yaml) use in creating your cluster using the following syntax:
-->
次のように、クラスターを作成するためのeksctlのデプロイメントファイル(eksworkshop.yaml)を作成します:

```bash
cat << EOF > eksworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}
  version: "1.17"

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 3
  ssh:
    allow: true
    publicKeyName: eksworkshop

# To enable all of the control plane logs, uncomment below:
# cloudWatch:
#  clusterLogging:
#    enableTypes: ["*"]

secretsEncryption:
  keyARN: ${MASTER_ARN}
EOF
```

<!--
Next, use the file you created as the input for the eksctl cluster creation.
-->
次に、作成したファイルを入力としてeksctl clusterを実行します。

```bash
eksctl create cluster -f eksworkshop.yaml
```

<!--
{{% notice info %}}
Launching EKS and all the dependencies will take approximately 15 minutes
{{% /notice %}}
-->
{{% notice info %}}
EKSと依存関係の起動にはおよそ15分程度かかります
{{% /notice %}}
