---
title: "GitHubアクセストークン"
date: 2018-10-087T08:30:11-07:00
weight: 13
draft: false
---

<!--
In order for CodePipeline to receive callbacks from GitHub, we need to generate a personal access token.
-->
GithubからCodePipelineがコールバックを受け取るためには、パーソナルアクセストークンを生成する必要があります。

<!--
Once created, an access token can be stored in a secure enclave and reused, so this step is only required
during the first run or when you need to generate new keys.
-->
生成されたら、アクセストークンは保護された場所に保存してください。この手順は最初の実行時のみ、もしくは新しい鍵を生成した時のみ必要です。

<!--
Open up the [New personal access page](https://github.com/settings/tokens/new) in GitHub.
-->
Githubで[New personal access page](https://github.com/settings/tokens/new)を開きます。

<!--
{{% notice info %}}
You may be prompted to enter your GitHub password
{{% /notice %}}
-->
{{% notice info %}}
Githubのパスワードを入れるように要求されるかもしれません
{{% /notice %}}

<!--
Enter a value for **Token description**, check the **repo** permission scope and scroll down and click the **Generate token** button
-->
**Tokenの説明** に何か記入し、 **repo** にチェックを入れ **Generate token** をクリックします

![Generate New](/images/codepipeline/github_token_name.png)

<!--
Copy the **personal access token** and save it in a secure place for the next step
-->
**personal access token** をコピーし、次の手順のために安全な場所に保管します。

![Generate New](/images/codepipeline/github_copy_access.png)
