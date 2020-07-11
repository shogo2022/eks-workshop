---
title: "デフォルトのpod間コミュニケーション"
date: 2018-08-07T08:30:11-07:00
weight: 2
---
<!--
In Kubernetes, the **pods by default can communicate with other pods**, regardless of which host they land on. Every pod gets its own IP address so you do not need to explicitly create links between pods. This is demonstrated by the **management-ui**.
-->
Kubernetesではどのホストにあろうとも、 **デフォルトではpod間のコミュニケーションが許可されています** 。すべてのpodは自身のIPアドレスを持つので明示的にリンクを作る必要はありません。これを **management-ui** でお見せします。

{{< output >}}
kind: Service
metadata:
  name: management-ui
  namespace: management-ui
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 9001
{{< /output >}}

<!--
To open the Management UI, retrieve the DNS name of the Management UI using:
-->
Management UIを開くには、Management UIのDNS名を以下のように取得します:

```
kubectl get svc -o wide -n management-ui
```

<!--
Copy the **EXTERNAL-IP** from the output, and paste into a browser.
The EXTERNAL-IP column contains a value that ends with "elb.amazonaws.com” - the full value is the DNS address.
-->
表示から **EXTERNAL-IP** をコピーして、ブラウザに貼り付けてください。
**EXTERNAL-IP** 列は"elb.amazonaws.com”で終わる値があり、その値全部がDNS名となります。

{{< output >}}
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP                                                              PORT(S)        AGE       SELECTOR
management-ui   LoadBalancer   10.100.239.7   a8b8c5f77eda911e8b1a60ea5d5305a4-720629306.us-east-1.elb.amazonaws.com   80:31919/TCP   9s        role=management-ui
{{< /output >}}

<!--
The UI here shows the default behavior, of all services being able to reach each other.
-->
ここに見えるように、UIはデフォルトの挙動、各サービスがそれぞれ通信できることを示しています。

![full access](/images/calico-full-access.png)
