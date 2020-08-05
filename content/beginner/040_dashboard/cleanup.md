---
title: "後片付け"
date: 2020-07-08T09:36:58-04:00
draft: false
weight: 90
tags:
  - beginner
  - CON203
---

<!--
Stop the proxy and delete the dashboard deployment
-->
プロキシを止め、ダッシュボードdeploymentを削除します

```bash
# kill proxy
pkill -f 'kubectl proxy --port=8080'

# delete dashboard
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml

unset DASHBOARD_VERSION
```
