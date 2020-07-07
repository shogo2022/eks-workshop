---
title: "AWSアカウントの作成"
chapter: false
weight: 1
---

<!--
{{% notice warning %}}
Your account must have the ability to create new IAM roles and scope other IAM permissions.
{{% /notice %}}
-->
{{% notice warning %}}
アカウントはIAMロールの新規作成とIAM権限の変更ができる必要があります。
{{% /notice %}}

<!--
1. If you don't already have an AWS account with Administrator access: [create
one now by clicking here](https://aws.amazon.com/getting-started/)
-->
1.  管理者権限を持ったAWSアカウントがない場合: [ここをクリックして作成してください](https://aws.amazon.com/getting-started/)

<!--
1. Once you have an AWS account, ensure you are following the remaining workshop steps
as an IAM user with administrator access to the AWS account:
[Create a new IAM user to use for the workshop](https://console.aws.amazon.com/iam/home?#/users$new)
-->
1. AWSアカウントが準備できたら、ワークショップの残りのステップはAWSアカウントに管理者権限があるIAMユーザで進めてください: 
[ワークショップで使うIAMユーザの作成](https://console.aws.amazon.com/iam/home?#/users$new)

<!--
1. Enter the user details:
![Create User](/images/iam-1-create-user.png)
-->
1. ユーザ詳細を入れます:
![Create User](/images/iam-1-create-user.png)

<!--
1. Attach the AdministratorAccess IAM Policy:
![Attach Policy](/images/iam-2-attach-policy.png)
-->
1. AdministratorAccess IAMポリシーをアタッチします:
![Attach Policy](/images/iam-2-attach-policy.png)

<!--
1. Click to create the new user:
![Confirm User](/images/iam-3-create-user.png)
-->
1. クリックして新規ユーザを作成します:
![Confirm User](/images/iam-3-create-user.png)

<!--
1. Take note of the login URL and save:
![Login URL](/images/iam-4-save-url.png)
-->
1. ログインURLを記録して、保存します:
![Login URL](/images/iam-4-save-url.png)