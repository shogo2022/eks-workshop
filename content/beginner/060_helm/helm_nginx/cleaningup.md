---
title: "クリーニング"
date: 2018-08-07T08:30:11-07:00
weight: 500
---

<!--
To remove all the objects that the Helm Chart created, we can use [Helm
uninstall](https://helm.sh/docs/helm/helm_uninstall/).
-->
Helmチャートが作成したオブジェクトを全て削除するには、[Helm
uninstall](https://helm.sh/docs/helm/helm_uninstall/)を使います。

<!--
Before we uninstall our application, we can verify what we have running via the
[Helm list](https://helm.sh/docs/helm/helm_list/) command:
-->
アプリケーションをアンインストールする前に、[Helm list](https://helm.sh/docs/helm/helm_list/)
コマンドで何が実行されているかを確認できます。

```sh
helm list
```

<!--
You should see output similar to below, which show that mywebserver is installed:
{{< output >}}
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
mywebserver     default         1               2020-02-18 22:02:13.844416354 +0100 CET deployed        nginx-5.1.6     1.16.1
{{< /output >}}
-->
次のような出力が出ているはずですが、mywebserverがインストールされているのがわかります:
{{< output >}}
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
mywebserver     default         1               2020-02-18 22:02:13.844416354 +0100 CET deployed        nginx-5.1.6     1.16.1
{{< /output >}}

<!--
It was a lot of fun; we had some great times sending HTTP back and forth, but now its time to uninstall this deployment.  To uninstall:
-->
面白かったですね; HTTPをやりとりするのは楽しいですが、deploymentをアンインストールする時間です。アンインストールするには次を実行します:

```sh
helm uninstall mywebserver
```

<!--
And you should be met with the output:
{{< output >}}
release "mywebserver" uninstalled
{{< /output >}}
-->
次のように表示されるはずです:
{{< output >}}
release "mywebserver" uninstalled
{{< /output >}}

<!--
kubectl will also demonstrate that our pods and service are no longer available:
-->
kubectlもpodやserviceeがなくなったことを示しています:

```sh
kubectl get pods -l app.kubernetes.io/name=nginx
kubectl get service mywebserver-nginx -o wide
```

<!--
As would trying to access the service via the web browser via a page reload.
-->
webブラウザ で更新をしてserviceに接続してみてください。

<!--
With that, cleanup is complete.
-->
これでクリーニングは完了です。