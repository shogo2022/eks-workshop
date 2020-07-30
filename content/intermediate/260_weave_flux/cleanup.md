---
title: "後片付け"
date: 2018-10-087T08:30:11-07:00
weight: 35
draft: false
---

<!--
Congratulations on completing the GitOps with Weave Flux module. 
-->
Weave FluxでのGitOpsモジュールは完了です。

<!--
This module is not used in subsequent steps, so you can remove the resources now, or at the end of the workshop.
-->
このモジュールは以降の手順では使わないので、リソースは削除しましょう。

<!--
First, delete all images from the [Amazon ECR Repository](https://console.aws.amazon.com/ecr/repositories).
-->
まずは[Amazon ECR Repository](https://console.aws.amazon.com/ecr/repositories)から全てのイメージを削除します。

<!--
Next, go to the [CloudFormation Console](https://console.aws.amazon.com/cloudformation/) and delete the stack used to deploy the image build CodePipeline
-->
次に[CloudFormationコンソール](https://console.aws.amazon.com/cloudformation/)でイメージビルドのCodePipelineのデプロイに使ったスタックを削除します

<!--
Now, delete Weave Flux and your load balanced services
-->
Weave Fluxとロードバランサーサービスを削除します

```
helm uninstall helm-operator --namespace flux
helm uninstall flux --namespace flux
kubectl delete namespace flux 
kubectl delete crd helmreleases.helm.fluxcd.io

helm uninstall mywebserver -n nginx
kubectl delete namespace nginx
kubectl delete svc eks-example -n eks-example
kubectl delete deployment eks-example -n eks-example
kubectl delete namespace eks-example
```

<!--
Optionally go to GitHub and delete your k8s-config and eks-example repositories.  
-->
自身のGitHubでk8s-configとeks-exampleレポジトリを削除します。

<!--
{{% notice info %}}
If you are using your own account for this workshop, continue with the below steps.  If doing this at an AWS event, skip the steps below.
{{% /notice %}}
-->
{{% notice info %}}
このワークショップに自身のアカウントを使っている場合は、次の手順に進んでください。AWSイベントの場合は、次の手順はスキップします。
{{% /notice %}}

<!--
Remove IAM roles you previously created 
-->
作成したIAMロールを削除します

```
aws iam delete-role-policy --role-name eksworkshop-CodePipelineServiceRole --policy-name codepipeline-access 
aws iam delete-role --role-name eksworkshop-CodePipelineServiceRole
aws iam delete-role-policy --role-name eksworkshop-CodeBuildServiceRole --policy-name codebuild-access 
aws iam delete-role --role-name eksworkshop-CodeBuildServiceRole
```

<!--
Remove the artifact bucket you previously created 
-->
アーティファクトを入れるバケットを削除します
```
ACCOUNT_ID=$(aws sts get-caller-identity | jq -r '.Account')
aws s3 rb s3://eksworkshop-${ACCOUNT_ID}-codepipeline-artifacts --force
```

