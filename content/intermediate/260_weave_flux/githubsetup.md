---
title: "GitHubの設定"
date: 2018-10-087T08:30:11-07:00
weight: 10
draft: false
---

<!--
We are going to create 2 GitHub repositories.  One will be used for a sample application that will trigger a Docker image build.  Another will be used to hold Kubernetes manifests that Weave Flux deploys into the cluster.  Note this is a pull based method compared to other continuous deployment tools that push to Kubernetes.  
-->
ここでは、2つのGitHubレポジトリを作成します。ひとつはサンプルアプリケーション用のものでDocker image buildを起動します。もう一つはWeave FluxがクラスタにデプロイをするKubernetesマニフェストを保持します。これはKubernetesに変更をpushする他の継続的デリバリーと違い、pullを使っていることを覚えておいてください。

<!--
Create the sample application repository by clicking [here](https://github.com/new).  
-->
[こちら](https://github.com/new)をクリックして、サンプルアプリケーションのレポジトリを作成します。

<!--
Fill in the form with repository name, description, and check initializing the repository with a README as shown below and click **Create repository**.
-->
下の図のようにレポジトリーの名前と説明の入力、そしてinitializing the repository with a READMEをチェックして **Create repository** を押します。

![Create Sample App Repository](/images/weave_flux/github_create_sample_app.png)

<!--
Repeat this process to create the Kubernetes manifests repositories by clicking [here](https://github.com/new).  Fill in the form as shown below and click **Create repository**.  
-->
続けて[こちら](https://github.com/new)から、Kubernetesマニフェストのレポジトリを作成します。下のように入力して、 **Create repository** をクリックします。

![Create Kubernetes Manifest Repository](/images/weave_flux/github_create_k8s.png)

<!--
The next step is to create a personal access token that will allow CodePipeline to receive callbacks from GitHub.  
Once created, an access token can be stored in a secure enclave and reused, so this step is only required
during the first run or when you need to generate new keys.
-->
次の手順は、GitHubからのコールバックをCodePipelineが受け取るようにするためのパーソナルアクセストークンの作成です。
アクセストークンが作成されたら、安全な場所に保存してください。この手順は最初の一回か、もしくは新しい鍵を生成した時のみ必要です。

<!--
Open up the [New personal access page](https://github.com/settings/tokens/new) in GitHub.
-->
GitHubの[New personal access page](https://github.com/settings/tokens/new)を開きます。

<!--
{{% notice info %}}
You may be prompted to enter your GitHub password
{{% /notice %}}
-->
{{% notice info %}}
GitHubのパスワードの入力を求められるかもしれません
{{% /notice %}}

<!--
Enter a value for **Token description**, check the **repo** permission scope and scroll down and click the **Generate token** button
-->
**Tokenの説明** を入力し、スコープの **repo** をチェックしたら、スクロールして **Generate token** ボタンをクリックします

![Generate New](/images/weave_flux/github_token_name.png)

<!--
Copy the **personal access token** and save it in a secure place for the next step
-->
**personal access token** をコピーして、次のステップで使うために安全な場所に保存します

![Generate New](/images/weave_flux/github_copy_access.png)

<!--
We will need to revisit GitHub one more time once we provision Weave Flux to enable Weave to control repositories.  However, at this time you can move on.  
-->
Weave Fluxを用意した後に、レポジトリへのアクセスを許可するためにもう一度GitHubにくる必要があります。ここでの作業は以上ですので、次に進みましょう。