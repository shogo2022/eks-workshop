---
title: "ワークスペースのIAM設定をアップデートする"
chapter: false
weight: 19
---

<!--
{{% notice info %}}
Cloud9 normally manages IAM credentials dynamically. This isn't currently compatible with
the EKS IAM authentication, so we will disable it and rely on the IAM role instead.
{{% /notice %}}
-->
{{% notice info %}}
Cloud9は通常IAMクレデンシャルを動的に管理します。現在、これはEKSのIAM認証と互換性がないため、
それを無効化し、代わりにIAMロールを使用します。
{{% /notice %}}

<!--
- Return to your workspace and click the gear icon (in top right corner), or click to open a new tab and choose "Open Preferences"
- Select **AWS SETTINGS**
- Turn off **AWS managed temporary credentials**
- Close the Preferences tab
![c9disableiam](/images/c9disableiam.png)
-->
- ワークスペースに戻って(右上の)歯車アイコンをクリックするか、新しいタブを開いて"Open Preferences"を選択してください。
- **AWS SETTINGS**を選択します。
- **AWS managed temporary credentials**を無効にします。
- Preferenceタブを閉じます。
![c9disableiam](/images/c9disableiam.png)

<!--
To ensure temporary credentials aren't already in place we will also remove
any existing credentials file:
```sh
rm -vf ${HOME}/.aws/credentials
```
-->
以前に使用した一時的なクレデンシャルが残らないように既存のクレデンシャルファイルを削除します:
```sh
rm -vf ${HOME}/.aws/credentials
```

<!--
We should configure our aws cli with our current region as default.
-->
aws cliが今のリージョンをデフォルトで使うように設定します。

<!--
{{% notice info %}}
If you are [at an AWS event](https://eksworkshop.com/020_prerequisites/aws_event/), ask your instructor which **AWS region** to use.
{{% /notice %}}
-->
{{% notice info %}}
[AWSイベントにいる場合](https://eksworkshop.com/020_prerequisites/aws_event/)は、インストラクターに**AWS region**を聞いてください。
{{% /notice %}}

```sh
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
```

<!-->
Check if AWS_REGION is set to desired region
```sh
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
```
-->
AWS_REGIONが指定したリージョンになったか確認します
```sh
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
```

<!--
Let's save these into bash_profile
```sh
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```
-->
これらをbash_profileに保存しましょう
```sh
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

<!--
### Validate the IAM role
-->
### IAMロールの確認

<!--
Use the [GetCallerIdentity](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) CLI command to validate that the Cloud9 IDE is using the correct IAM role.
-->
[GetCallerIdentity](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) CLIコマンドを使ってCloud9のIDEが正しいIAMロールを使っているか確認します。

```
aws sts get-caller-identity --query Arn | grep eksworkshop-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

<!--
First, get the IAM role name from the AWS CLI.
```bash
INSTANCE_PROFILE_NAME=`basename $(aws ec2 describe-instances --filters Name=tag:Name,Values=aws-cloud9-${C9_PROJECT}-${C9_PID} | jq -r '.Reservations[0].Instances[0].IamInstanceProfile.Arn' | awk -F "/" "{print $2}")`
aws iam get-instance-profile --instance-profile-name $INSTANCE_PROFILE_NAME --query "InstanceProfile.Roles[0].RoleName" --output text
```
-->

<!--
If the IAM role is not valid, <span style="color: red;">**DO NOT PROCEED**</span>. Go back and confirm the steps on this page.
-->
IAMロールが有効でない場合は、 <span style="color: red;">**進まないでください**</span> 。戻ってこのページの手順を確認してください。