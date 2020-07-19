---
title: "Wordpressへのアクセス"
chapter: false
weight: 3
---

<!--
### Testing public URL
-->
### パブリックURLでテスト

<!--
{{% notice warning %}}
It may take a few minutes for the LoadBalancer to be available.
{{% /notice %}}
-->
{{% notice warning %}}
LoadBalancerが準備できるまでに数分かかります。
{{% /notice %}}

<!--
You’ll need the URL for your WordPress site. This is easily accomplished by running the command below from your terminal window.
-->
WordPressサイトのURLが必要です。次のコマンドをターミナルで実行してください。

```bash
export SERVICE_URL=$(kubectl get svc -n wordpress-cwi understood-zebu-wordpress --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")

echo "Public URL: http://$SERVICE_URL/"
```

<!--
You should see the _Hello World_ WordPress welcome page.
-->
_Hello World_ のWordPressウェルカムページが表示されるはずです。
![WP Hello World](/images/ekscwci/wp_hello_world.png)

<!--
### Testing the admin interface
-->
### adminインタフェースのテスト

```bash
export ADMIN_URL="http://$SERVICE_URL/admin"
export ADMIN_PASSWORD=$(kubectl get secret --namespace wordpress-cwi understood-zebu-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode)

echo "Admin URL: http://$SERVICE_URL/admin
Username: user
Password: $ADMIN_PASSWORD
"
```

<!--
In your favorite browser paste in your Wordpress Admin URL from the Installing Wordpress section. You should be greeted with the following screen.
-->
前のセクションで入手したWordpresss Admin URLをブラウザで開きます。次のページが表示されるはずです。
![WP Login Page](/images/ekscwci/wploginpage.png)

<!--
Enter your username and password to make sure they work.
-->
ユーザ名とパッスワードを入れ、動くことを確認します。

<!--
If you are taken to the below screen, you have a successfully running Wordpress install backed by MaiaDB in your EKS Cluster.
-->
次の画面が出れば、EKSクラスタの中にWordpressとMariaDBが正常に稼働しています。

![Wordpress Dashboard](/images/ekscwci/wpdashboard.png)

<!--
Now that we have verified that the site is working we can continue with getting CloudWatch Container Insights installed on our cluster!
-->
サイトが正常に動くことを確認したので、CloudWatch Container Insightsをクラスタにインストールしましょう!