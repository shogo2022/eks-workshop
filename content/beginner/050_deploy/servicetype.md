---
title: "サービスタイプの確認"
date: 2018-09-18T17:40:09-05:00
weight: 25
---

<!--
Before we bring up the frontend service, let's take a look at the service types
we are using:
This is `kubernetes/service.yaml` for our frontend service:
{{< output >}}
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-frontend
spec:
  selector:
    app: ecsdemo-frontend
  type: LoadBalancer
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
{{< /output >}}
-->
フロントエンドサービスを起動する前に、私たちが使うサービスタイプをみてみましょう:
これはフロントエンドサービスの `kubernetes/service.yaml` です:
{{< output >}}
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-frontend
spec:
  selector:
    app: ecsdemo-frontend
  type: LoadBalancer
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
{{< /output >}}

<!--
Notice `type: LoadBalancer`: This will configure an ELB to handle incoming traffic
to this service.
-->
`type: LoadBalancer` に注意してください: これはサービスへのトラフィックに対してELBを使うように設定します。

<!--
Compare this to `kubernetes/service.yaml` for one of our backend services:
{{< output >}}
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-nodejs
spec:
  selector:
    app: ecsdemo-nodejs
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
{{< /output >}}
-->
これをバックエンドサービスの `kubernetes/service.yaml` と比べてください:
{{< output >}}
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-nodejs
spec:
  selector:
    app: ecsdemo-nodejs
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
{{< /output >}}

<!--
Notice there is no specific service type described. When we check [the kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)
we find that the default type is `ClusterIP`. This Exposes the service on a cluster-internal IP.
Choosing this value makes the service only reachable from within the cluster.
-->
特定のサービスタイプが設定されていないことに気づきましたか。[kubernetesのドキュメント](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)
によるとデフォルトのタイプは `ClusterIP` となっています。これはサービスをクラスターの内部IPで公開します。
これを選ぶと、そのサービスはクラスター内からのみ接続できるようになります。