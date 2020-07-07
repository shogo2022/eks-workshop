---
title: "ワークスペースの作成"
chapter: false
weight: 14
---

<!--
{{% notice warning %}}
The Cloud9 workspace should be built by an IAM user with Administrator privileges,
not the root account user. Please ensure you are logged in as an IAM user, not the root
account user.
{{% /notice %}}
-->
{{% notice warning %}}
Cloud9のワークスペースはrootアカウントではなく、管理者権限のあるIAMユーザで作成される必要があります。
rootアカウントユーザではなく、IAMユーザとしてログインしていることを確認してください。
{{% /notice %}}

<!---
{{% notice info %}}
This workshop was designed to run in the **Oregon (us-west-2)** region. **Please don't
run in any other region.** Future versions of this workshop will expand region availability,
and this message will be removed.
{{% /notice %}}
-->

<!--
{{% notice tip %}}
Ad blockers, javascript disablers, and tracking blockers should be disabled for
the cloud9 domain, or connecting to the workspace might be impacted.
Cloud9 requires third-party-cookies. You can whitelist the [specific domains]( https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).
{{% /notice %}}
-->
{{% notice tip %}}
Adブロッカー、javascript無効化ツール、そしてトラッキングブロッカーはcloud9ドメインに対して無効化してください、
ワークスペースへの接続に影響が出る場合があります。
Cloud9はサードパーティクッキーを使います。[特定のドメイン]( 
https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading)をホワイトリストできます。
{{% /notice %}}

<!--
### Launch Cloud9 in your closest region:
{{< tabs name="Region" >}}
{{{< tab name="Oregon" include="us-west-2.md" />}}
{{{< tab name="Ireland" include="eu-west-1.md" />}}
{{{< tab name="Ohio" include="us-east-2.md" />}}
{{{< tab name="Singapore" include="ap-southeast-1.md" />}}
{{< /tabs >}}
-->
### 一番近いリージョンでCloud9を起動:
{{< tabs name="Region" >}}
{{{< tab name="Oregon" include="us-west-2.md" />}}
{{{< tab name="Ireland" include="eu-west-1.md" />}}
{{{< tab name="Ohio" include="us-east-2.md" />}}
{{{< tab name="Singapore" include="ap-southeast-1.md" />}}
{{< /tabs >}}

<!--
- Select **Create environment**
- Name it **eksworkshop**, click Next.
- Choose **"t3.small"** for instance type, take all default values and click **Create environment**
- When it comes up, customize the environment by closing the **welcome tab**
and **lower work area**, and opening a new **terminal** tab in the main work area:
![c9before](/images/c9before.png)
-->
- **Create environment**を選択
- **eksworkshop**と名前をつけ、Nextをクリック
- インスタンスタイプに**"t3.small"**を選択し、デフォルトのまま**Create environment**をクリック
- 起動したら、環境をカスタマイズします。**welcome tab**と**下部のワークエリア**を閉じ、新しく**terminal**タブをワークエリアに開きます:
![c9before](/images/c9before.png)

<!--
- Your workspace should now look like this:
![c9after](/images/c9after.png)
-->
- ワークショップはこのようになっているはずです:
![c9after](/images/c9after.png)


<!--
- If you like this theme, you can choose it yourself by selecting **View / Themes / Solarized / Solarized Dark**
in the Cloud9 workspace menu.
-->
- このテーマが気に入ったら、Cloud9のワークスペースメニューから**View / Themes / Solarized / Solarized Dark**と選択することで選ぶことができます。