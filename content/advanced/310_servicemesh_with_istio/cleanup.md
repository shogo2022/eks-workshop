---
title: "後片付け"
date: 2019-03-20T13:59:44+01:00
weight: 70
draft: false
---

<!--
To cleanup, follow the below steps.
-->
以下の手順で片付けを行います。

<!--
Terminate any `kubectl port-forward` or `watch` processes
-->
全ての `kubectl port-forwarrd` と `watch`のプロセスを終了させます 

```bash
killall kubectl
killall watch
```

<!--
When you’re finished experimenting with the `Bookinfo` sample, uninstall and clean it up using the following instructions
-->
`Bookinfo` でのハンズオンが完了したら、次の手順に沿ってアンインストールと片付けを行います

```bash
export NAMESPACE="bookinfo"

${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/platform/kube/cleanup.sh

kubectl delete ns bookinfo
```

`istioctl` will delete:

* The RBAC permissions
* The `istio-system` namespace
* All resources hierarchically under it

{{% notice info %}}
<!--
you can ignore the errors for non-existent resources because they may have been deleted hierarchically.
-->
存在しないリソース(non-existent resources)のエラーは、階層的に削除されているので、無視してください。
{{% /notice %}}

```bash
istioctl manifest generate --set profile=demo | kubectl delete -f -
```

<!--
Finally, we can delete the istio folder and clean up the `~/.bash_profile`.
-->
最後にistioフォルダを削除し、 `~/.bash_profile` をきれいにします。

```bash
cd ~/environment
rm -rf istio-${ISTIO_VERSION}

sed -i '/ISTIO_VERSION/d' ${HOME}/.bash_profile
unset ISTIO_VERSION
```
