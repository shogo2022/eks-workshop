---
title: "Chartレポジトリをアップデートする"
date: 2018-08-07T08:30:11-07:00
weight: 100
---

<!--
Helm uses a packaging format called
[Charts](https://helm.sh/docs/topics/charts/). A Chart is a collection of files
and templates that describes Kubernetes resources.
-->
Helmは[Charts](https://helm.sh/docs/topics/charts/)と呼ばれるパッケージ形式を使っています。
ChartはKubernetesリソースを記述したファイルやテンプレートの集合です。

<!--
Charts can be simple, describing something like a standalone web server (which
is what we are going to create), but they can also be more complex, for example,
a chart that represents a full web application stack, including web servers,
databases, proxies, etc.
-->
Chartは(私たちがやろううとしているように)スタンドアローンのWEBサーバのようなシンプルなものも記述できますし、
もっと複雑なWEBサーバ、データベース、プロキシなどを含む完全なWEBアプリケーションスタックを表すこともできます。

<!--
Instead of installing Kubernetes resources manually via `kubectl`, one can use
Helm to install pre-defined Charts faster, with less chance of typos or other
operator errors.
-->
Kubernetesリソースを`kubectl`でマニュアルにインストールする代わりに、Helmを使って事前に定義されたChartを使うことで
より早くインストールでき、誤字やエラーを減らすことができます。

<!--
Chart repositories change frequently due to updates and new additions.  To keep
Helm's local list updated with all these changes, we need to occasionally run
the [repository update](https://helm.sh/docs/helm/helm_repo_update) command.
-->
アップデートや追加によりChartレポジトリは頻繁に変わります。ローカルのHelmリストに
これらの変更がアップデートされるために、時々[repository update](https://helm.sh/docs/helm/helm_repo_update)
コマンドを実行する必要があります。

<!--
To update Helm's local list of Charts, run:
-->
HelmのChartのローカルリストをアップデートするには、以下を実行します:

<!--
```
# first, add the default repository, then update
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
```
-->
```
#  まず、デフォルトレポジトリを追加し、アップデートします。
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
```

<!--
And you should see something similar to:
-->
次のような表示が出てくるはずです:

{{< output >}}
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
{{< /output >}}

<!--
Next, we'll search for the nginx web server Chart.
-->
次にnginx webサーバのChartを探しましょう。