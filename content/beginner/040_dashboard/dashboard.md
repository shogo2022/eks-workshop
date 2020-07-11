---
title: "公式のKubernetesダッシュボードのデプロイ"
date: 2018-08-07T08:30:11-07:00
weight: 10
---

<!--
The official Kubernetes dashboard is not deployed by default, but there are
instructions in [the official documentation](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
-->
公式のKubernetesダッシュボードはデフォルトでは設定されていませんが、[公式ドキュメント](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)に説明があります。

<!--
We can deploy the dashboard with the following command:

```bash
export DASHBOARD_VERSION="v2.0.0"

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml
```
-->
ダッシュボードは以下のコマンドでデプロイできます:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
```

<!--
Since this is deployed to our private cluster, we need to access it via a proxy.
`kube-proxy` is available to proxy our requests to the dashboard service.  In your
workspace, run the following command:

```bash
kubectl proxy --port=8080 --address=0.0.0.0 --disable-filter=true &
```
-->
これはプライベートなクラスターにデプロイされているので、プロキシを使う必要があります。
Kube-proxyを使うことでダッシュボードサービスへのリクエストをプロキシさせることができます。
自分のワークスペースで次のコマンドを実行してください:
```
kubectl proxy --port=8080 --address=0.0.0.0 --disable-filter=true &
```

<!--
This will start the proxy, listen on port 8080, listen on all interfaces, and
will disable the filtering of non-localhost requests.
-->
これはプロキシを起動し、8080番ポート、全てのインタフェースで待ち受けます。そして、ローカルホスト以外からの
リクエストフィルターを無効化します。

<!--
This command will continue to run in the background of the current terminal's session.
-->
このコマンドは現在のターミナルセッション中はバックグラウンドで動き続けます。

<!--
{{% notice warning %}}
We are disabling request filtering, a security feature that guards against XSRF attacks.
This isn't recommended for a production environment, but is useful for our dev environment.
{{% /notice %}}
-->
{{% notice warning %}}
ここではXSRFアタックを防ぐセキュリティ機能であるリクエストフィルタを無効化しています。
これは商用環境では推奨されませんが、この開発環境では有用です。
{{% /notice %}}
