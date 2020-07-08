---
title: "サービスのテスト"
date: 2018-08-07T08:30:11-07:00
weight: 40
---

<!--
To test the service our eksdemo Chart created, we'll need to get the name of the ELB endpoint that was generated when we deployed the Chart:
-->
eksdemo Chartで作成されたサービスをテストするには、Chartがデプロイされた時に精製されたELBエンドポイントの名前が必要です:

```sh
kubectl get svc ecsdemo-frontend -o jsonpath="{.status.loadBalancer.ingress[*].hostname}"; echo
```

<!--
Copy that address, and paste it into a new tab in your browser.  You should see something similar to:
-->
アドレスをコピーして、ブラウザの新しいタブにペーストします。次のようなものが見えるはずです:

![Example Service](/images/helm_micro/micro_example.png)
