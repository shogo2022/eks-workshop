---
title: "バックエンドにポリシー適用"
weight: 210
draft: true
---

<!--
Now, let's create a policy that allows the _ecsdemo-frontend_ microservice to communicate with the _ecsdemo-nodejs_ microservice. That policy will look something like this:
-->
では、 _ecsdemo-frontend_ マイクロサービスが _ecsdemo-nodejs_ マイクロサービスと通信できるようなポリシーを作成しましょう。そのポリシーは次のようになります：

{{< output >}}
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ecsdemo-nodejs
spec:
  podSelector:
    matchLabels:
      app: ecsdemo-nodejs
ingress:
- ports:
  -port: 3000
  from:
  - podSelector:
    matchLabels:
      app: ecsdemo-frontend

{{< /output >}}

<!--
Let's create that policy in a file, called _ecsdemo-nodejs-policy.yaml_ and then load it into Kubernetes using kubectl.
-->
このポリシーで _ecsdemo-nodejs-policy.yaml_ というファイルを作成して、kubectlでKubernetesに投入しましょう。

```
kubectl apply -f ecsdemo-nodejs-policy.yaml
```

<!--
Once you've done that, again, look at the flow logs and you will see that traffic between the frontend and the nodejs services is now being allowed, but the traffic from the frontend to the crystal microservice is still being blocked.  Let's fix that.
-->
完了したら、フローログを確認します。フロントエンドからnodejsサービスへは許可されていますが、フロントエンドからcrystalマイクロサービスへはまだ許可されていません。これを直しましょう。

{{< output >}}
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ecsdemo-crystal
spec:
  podSelector:
    matchLabels:
      app: ecsdemo-crystal
ingress:
- ports:
  -port: 3000
  from:
  - podSelector:
    matchLabels:
      app: ecsdemo-frontend
{{< /output >}}

<!--
Use the same file create and kubectl apply steps that we used above to apply this new policy, and now you will see that all the frontend to backend traffic is now being allowed, again.
-->
ファイルを同じように作り、ポリシーを適用するためにこれまでと同じようにkubectl applyを実施してください。これで、全てのフロントエンドからバックエンドへのサービスが再び許可されました。