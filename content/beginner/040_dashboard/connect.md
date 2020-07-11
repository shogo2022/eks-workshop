---
title: "ダッシュボードへの接続"
date: 2018-08-07T08:30:11-07:00
weight: 30
---

<!--
Now we can access the Kubernetes Dashboard
-->
これでKubernetesダッシュボードに接続できます

<!--
1. In your Cloud9 environment, click **Tools / Preview / Preview Running Application**
1. Scroll to **the end of the URL** and append:
-->
1. Cloud9環境で、 **Tools / Preview / Preview Running Application** をクリックしてください
1. **URLの最後までスクロールして** 、以下を付け加えます

```text
/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

<!--
The Cloud9 Preview browser doesn't appear to support the token authentication, so once you have the login screen in the cloud9 preview browser tab, press the **Pop Out** button to open the login screen in a regular browser tab, like below:
![popout](/images/popout.png)
-->
Cloud9のプレビューブラウザはtoken認証をサポートしていないようなので、Cloud9のプレビューブラウザでログインが表示されたら **Pop Out** を押して、以下のように標準ブラウザでログインページを表示させます:
![popout](/images/popout.png)

<!--
Open a New Terminal Tab  and enter

```bash
aws eks get-token --cluster-name eksworkshop-eksctl | jq -r '.status.token'
```
-->
新しいターミナルタブを開き、以下を入力します
```
aws eks get-token --cluster-name eksworkshop-eksctl | jq -r '.status.token'
```

<!--
Copy the output of this command and then click the radio button next to
*Token* then in the text field below paste the output from the last command.
-->
このコマンドの出力結果をコピーし、*Token*の横のラジオボタンをクリックし、
テキストフィールドにペーストします。

![Token page](/images/dashboard-connect.png)

<!--
Then press *Sign In*.
-->
そして、*Sign In*を押します。