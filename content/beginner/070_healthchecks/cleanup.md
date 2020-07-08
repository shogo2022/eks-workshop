---
title: "クリーニング"
chapter: false
weight: 25
---
<!--
Our Liveness Probe example used HTTP request and Readiness Probe executed a command to check health of a pod. Same can be accomplished using a TCP request as described in the [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).
```
kubectl delete -f ~/environment/healthchecks/liveness-app.yaml
kubectl delete -f ~/environment/healthchecks/readiness-deployment.yaml
```
-->
ここではライブネスプローブはHTTPリクエストを使い、レディネスプローブはコマンドを実行してpodの健康状態を確認していました。[ドキュメント](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)にある通り、同じことはTCPリクエストでも可能です。
```
kubectl delete -f ~/environment/healthchecks/liveness-app.yaml
kubectl delete -f ~/environment/healthchecks/readiness-deployment.yaml
```