---
title: "Kubeflowダッシュボード"
date: 2019-08-22T00:00:00-08:00
weight: 20
draft: false
---

<!--
### Kubeflow Dashboard
-->
### Kubeflowダッシュボード

<!--
Use **port-forward** to access Kubeflow dashboard
-->
**port-forward** を使ってKubeflowダッシュボードにアクセス

```
kubectl port-forward svc/istio-ingressgateway -n istio-system 8080:80
```
<!--
In your Cloud9 environment, click **Tools / Preview / Preview Running Application** to access dashboard. You can click on Pop out window button to maximize browser into new tab.
-->
Cloud9で、 **Tools / Preview / Preview Running Application** をクリックしダッシュボードを開きます。ポップアウトボタンを押すと、新しいタブで最大化することができます。

<!--
Leave the current terminal running because if you kill the process, you will loose access to the dashboard. Open new Terminal to follow rest of the workshop
-->
プロセスが終わるとダッシュボードへのアクセスがなくなるため、現在のターミナルはそのままにしておきます。以降の手順は新しいターミナルで実行してください

![dashboard](/images/kubeflow/dashboard-welcome.png)

<!--
Click on **Start Setup**
-->
**Start Setup** をクリック

<!--
Specify the namespace as **eksworkshop**
-->
名前空間に **eksworkshop** を指定

![dashboard](/images/kubeflow/dashboard-create-namespace.png)

<!--
Click on **Finish** to view the dashboard
-->
**Finish** をクリックしてダッシュボードを確認する

![dashboard](/images/kubeflow/dashboard-first-look.png)
