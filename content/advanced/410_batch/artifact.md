---
title: "アーティファクトのレポジトリ設定"
date: 2018-11-18T00:00:00-05:00
weight: 50
draft: false
---

<!--
### Configure Artifact Repository
-->
### アーティファクトレポジトリの設定

<!--
Argo uses an artifact repository to pass data between jobs in a workflow, known as artifacts. Amazon S3 can be used as an artifact repository.
-->
Argoはワークフローのジョブ間でのデータをやりとりするのにアーティファクトレポジトリを使います。アーティファクトレポジトリにはAmazon S3を使うことができます。

<!--
Let's create a S3 bucket using the AWS CLI.
-->
AWS CLIでS3バケットを作成しましょう。

```bash
aws s3 mb s3://batch-artifact-repository-${ACCOUNT_ID}/
```

<!--
Next, we will add this bucket as an argo `artifactRepository` in the configmap `workflow-controller-configmap`
-->
次に、このバケットを `workflow-controller-configmap` のconfigmapの中で `artifactRepository` として追加します。

<!--
Create the patch
-->
パッチの作成

```bash
cat <<EoF > ~/environment/batch_policy/argo-patch.yaml
data:
  config: |
    artifactRepository:
      s3:
        bucket: batch-artifact-repository-${ACCOUNT_ID}
        endpoint: s3.amazonaws.com
EoF
```

<!--
deploy the patch
-->
パッチのデプロイ

```bash
kubectl -n argo patch \
  configmap/workflow-controller-configmap \
  --patch "$(cat ~/environment/batch_policy/argo-patch.yaml)"
```

<!--
Let's verify the configmap
-->
configmapを確認しましょう

```bash
kubectl -n argo get configmap/workflow-controller-configmap -o yaml
```

<!--
Output Example
-->
表示例

{{< output >}}
apiVersion: v1
data:
  config: |-
    artifactRepository:
      s3:
        bucket: batch-artifact-repository-197520326489
        endpoint: s3.amazonaws.com
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"name":"workflow-controller-configmap","namespace":"argo"}}
  creationTimestamp: "2020-07-07T19:07:48Z"
  name: workflow-controller-configmap
  namespace: argo
  resourceVersion: "1082653"
  selfLink: /api/v1/namespaces/argo/configmaps/workflow-controller-configmap
  uid: a1accd9e-c528-41a3-b811-226f5662c446
{{< /output >}}

<!--
### Create an IAM Policy
-->
### IAMポリシーの作成

<!--
In order for Argo to read from/write to the S3 bucket, we need to configure an inline policy and add it to the EC2 instance profile of the worker nodes.
-->
ArgoがS3バケットに対して読み込みや書き込みを行うためにはワーカーノードのEC2インスタンスプロファイルにインラインポリシーを設定する必要があります。

<!--
First, we will need to ensure the Role Name our workers use is set in our environment:
-->
最初に、環境変数にワーカーが使っているRole名が設定されていることを確認します:

```bash
test -n "$ROLE_NAME" && echo ROLE_NAME is "$ROLE_NAME" || echo ROLE_NAME is not set
```

<!--
If you receive an error or empty response, expand the steps below to export.
-->
エラーが表示されたり、何も表示されない場合は、下の手順を展開してexportします。

<!--
{{%expand "Expand here if you need to export the Role Name" %}}
If `ROLE_NAME` is not set, please review: [/030_eksctl/test/](/030_eksctl/test/)
{{% /expand %}}
-->
{{%expand "Role名をexportするにはこちら" %}}
`ROLE_NAME` が設定されていない場合は、ここを参照してください: [/030_eksctl/test/](/030_eksctl/test/)
{{% /expand %}}

<!--
{{< output >}}
# Example Output
ROLE_NAME is eksctl-eksworkshop-eksctl-nodegro-NodeInstanceRole-RPDET0Z4IJIF
{{< /output >}}
-->
{{< output >}}
# 表示例
ROLE_NAME is eksctl-eksworkshop-eksctl-nodegro-NodeInstanceRole-RPDET0Z4IJIF
{{< /output >}}

<!--
Create and policy and attach to the worker node role.
-->
ポリシーを作成し、ワーカーノードのroleに紐づけます。

```bash
cat <<EoF > ~/environment/batch_policy/k8s-s3-policy.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*"
      ],
      "Resource": [
        "arn:aws:s3:::batch-artifact-repository-${ACCOUNT_ID}",
        "arn:aws:s3:::batch-artifact-repository-${ACCOUNT_ID}/*"
      ]
    }
  ]
}
EoF

aws iam put-role-policy --role-name $ROLE_NAME --policy-name S3-Policy-For-Worker --policy-document file://~/environment/batch_policy/k8s-s3-policy.json
```

<!--
Validate that the policy is attached to the role
-->
roleにポリシーが紐づいていることを確認します

```bash
aws iam get-role-policy --role-name $ROLE_NAME --policy-name S3-Policy-For-Worker
```
