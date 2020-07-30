---
title: "前提条件"
date: 2018-10-14T19:56:14-04:00
weight: 5
draft: false
---

<!--
#### Is **helm** installed?
-->
#### **helm** がインストールされているか?

<!--
We will use **helm** to install Weave Flux and a sample Helm chart. Check to see if `helm` is installed:
-->
ここでは **helm** を使ってWeave FluxとサンプルのHelmチャートをインストールします。 `helm` がインストールされているか確認しましょう:

```
helm version
```
<!--
If `helm` is not found, see [installing helm](/beginner/060_helm/helm_intro/install/index.html) for instructions.
-->
`helm` が見つからない場合は、[helmのインストール](/beginner/060_helm/helm_intro/install/index.html)を参照してください。

<br>
<!--
#### Does S3 artifact bucket exist and are IAM service roles created?
-->
#### S3バケットがあり、IAMサービスロールが作成されているか?

<!--
AWS CodePipeline and AWS CodeBuild both need [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/) service roles to create a Docker image build pipeline.  
-->
AWS CodePipelineとAWS CodeBuildは共に、Docker image build pipeline作成のために[AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/)が必要です。

<!--
In this step, we are going to create an IAM role and add an inline policy that we will use in the CodeBuild stage
to interact with the EKS cluster via kubectl.
-->
この手順ではCodeBuildステージで、kubectlがEKSクラスタとやりとりをする際に使用するインラインポリシーを持ったIAMロールを作成します。

<!--
Create the bucket and roles:
-->
バケットとロールの作成:

```
# Use your account number below
ACCOUNT_ID=$(aws sts get-caller-identity | jq -r '.Account')
aws s3 mb s3://eksworkshop-${ACCOUNT_ID}-codepipeline-artifacts

cd ~/environment

wget https://eksworkshop.com/intermediate/260_weave_flux/iam.files/cpAssumeRolePolicyDocument.json

aws iam create-role --role-name eksworkshop-CodePipelineServiceRole --assume-role-policy-document file://cpAssumeRolePolicyDocument.json 

wget https://eksworkshop.com/intermediate/260_weave_flux/iam.files/cpPolicyDocument.json

aws iam put-role-policy --role-name eksworkshop-CodePipelineServiceRole --policy-name codepipeline-access --policy-document file://cpPolicyDocument.json

wget https://eksworkshop.com/intermediate/260_weave_flux/iam.files/cbAssumeRolePolicyDocument.json

aws iam create-role --role-name eksworkshop-CodeBuildServiceRole --assume-role-policy-document file://cbAssumeRolePolicyDocument.json 

wget https://eksworkshop.com/intermediate/260_weave_flux/iam.files/cbPolicyDocument.json

aws iam put-role-policy --role-name eksworkshop-CodeBuildServiceRole --policy-name codebuild-access --policy-document file://cbPolicyDocument.json
```
