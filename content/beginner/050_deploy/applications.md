---
title: "サンプルアプリケーションのデプロイ"
date: 2018-09-18T16:01:14-05:00
weight: 5
---

{{< output "linenos=true" >}}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ecsdemo-nodejs
  labels:
    app: ecsdemo-nodejs
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ecsdemo-nodejs
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: ecsdemo-nodejs
    spec:
      containers:
      - image: brentley/ecsdemo-nodejs:latest
        imagePullPolicy: Always
        name: ecsdemo-nodejs
        ports:
        - containerPort: 3000
          protocol: TCP
{{< /output >}}

<!--
In the sample file above, we describe the service and  *how* it should be deployed.
We will write this description to the kubernetes api using kubectl, and kubernetes
will ensure our preferences are met as the application is deployed.
-->
上のサンプルファイルには、サービスとそれが　*どのように*　デプロイされるべきかが記載されています。
この宣言はkubectlを通じてkubernetesのapiに書かれます。そしてKubernetesはこれに沿って
アプリケーションをデプロイします。

<!--
The containers listen on port 3000, and native service discovery will be used
to locate the running containers and communicate with them.
-->
コンテナはポート3000で待ち受けていて、実行中のコンテナの発見とやりとりには
にはネイティブのサービスディスカバリーが使用されます。
