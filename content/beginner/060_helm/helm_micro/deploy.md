---
title: "eksdemo Chartをデプロイ"
date: 2018-08-07T08:30:11-07:00
weight: 30
---

<!--
#### Use the dry-run flag to test our templates
-->
#### dry-runフラグでテンプレートをテスト

<!--
To test the syntax and validity of the Chart without actually deploying it,
we'll use the `--dry-run` flag.
-->
Chartを実際にデプロイすることなく、文法や有効性をテストするのには、
`--dry-run`フラグが使えます。

<!--
The following command will build and output the rendered templates without
installing the Chart:
-->
次のコマンドはChartをインストールすることなく、テンプレートをレンダーして表示します:

```sh
helm install --debug --dry-run workshop ~/environment/eksdemo
```

<!--
Confirm that the values created by the template look correct.
-->
テンプレートの値が正しいか確認します。

<!--
#### Deploy the chart
-->
#### chartのデプロイ

<!--
Now that we have tested our template, let's install it.
-->
テンプレートのテストが終わったので、インストールしましょう。

```sh
helm install workshop ~/environment/eksdemo
```

<!--
Similar to what we saw previously in the [nginx Helm Chart
example](/beginner/060_helm/helm_nginx/index.html), an output of the command will contain the information about the deployment status, revision, namespace, etc, similar to:
-->
前回の[nginx Helm Chartの例](/beginner/060_helm/helm_nginx/index.html)と同じように、以下のようなデプロイメント状況、リビジョン番号、ネームスペースなどが表示されます:

{{< output >}}
NAME: workshop
LAST DEPLOYED: Tue Feb 18 22:11:37 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
{{< /output >}}

<!--
In order to review the underlying services, pods and deployments, run:
```sh
kubectl get svc,po,deploy
```
-->
実際のservice、podやdeploymentを見るには、次のコマンドを実行します:
```sh
kubectl get svc,po,deploy
```
