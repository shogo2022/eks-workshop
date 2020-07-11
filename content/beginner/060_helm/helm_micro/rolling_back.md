---
title: "ロールバック"
date: 2018-08-07T08:30:11-07:00
weight: 50
---

<!--
Mistakes will happen during deployment, and when they do, Helm makes it easy to undo, or "roll back" to the previously deployed version.
-->
デプロイに間違いはつきものです、起こった際には、Helmで取り消し、もしくは以前のバージョンに"ロールバック"が簡単にできます。

<!--
#### Update the demo application chart with a breaking change
-->
#### デモアプリケーションに間違ったアップデートを加える

<!--
Open **values.yaml** and modify the image name under `nodejs.image` to **brentley/ecsdemo-nodejs-non-existing**. This image does not exist, so this will break our deployment.
-->
**values.yaml** を開き、 `nodejs.image` の名前を **rentley/ecsdemo-nodejs-non-existing** に変更します。
このイメージは存在しないものなので、deploymentは動かなくなります。

<!--
Deploy the updated demo application chart:
```sh
helm upgrade workshop ~/environment/eksdemo
```
-->
アップデートされたデモアプリケーションchartをデプロイします:
```sh
helm upgrade workshop ~/environment/eksdemo
```

<!--
The rolling upgrade will begin by creating a new nodejs pod with the new image. The new `ecsdemo-nodejs` Pod should fail to pull non-existing image. Run `kubectl get pods` to see the `ImagePullBackOff` error.
-->
新しいイメージを使った新しいnodsjsのポッドが作成され、ローリングアップグレードが始まります。新しい `ecsdemo-nodejs` podはイメージが存在しないため失敗するはずです。 `kubectl get pods` を実行して、 `ImagePullBackOff` エラーが出ていることを確認しましょう。

```sh
kubectl get pods
```
{{< output >}}
NAME                               READY   STATUS             RESTARTS   AGE
ecsdemo-crystal-844d84cb86-56gpz   1/1     Running            0          23m
ecsdemo-crystal-844d84cb86-5vvcg   1/1     Running            0          23m
ecsdemo-crystal-844d84cb86-d2plf   1/1     Running            0          23m
ecsdemo-frontend-6df6d9bb9-dpcsl   1/1     Running            0          23m
ecsdemo-frontend-6df6d9bb9-lzlwh   1/1     Running            0          23m
ecsdemo-frontend-6df6d9bb9-psg69   1/1     Running            0          23m
ecsdemo-nodejs-6fdf964f5f-6cnzl    1/1     Running            0          23m
ecsdemo-nodejs-6fdf964f5f-fbcjv    1/1     Running            0          23m
ecsdemo-nodejs-6fdf964f5f-v88jn    1/1     Running            0          23m
ecsdemo-nodejs-7c6575b56c-hrrsp    0/1     ImagePullBackOff   0          15m
{{< /output >}}

<!--
Run `helm status workshop` to verify the `LAST DEPLOYED` timestamp. 
-->
`helm status workshop` を実行して `LAST DEPLOYED` のタイムスタンプを確認します。

```sh
helm status workshop
```

{{< output >}}
LAST DEPLOYED: Tue Feb 18 22:14:00 2020
NAMESPACE: default
STATUS: deployed
...
{{< /output >}}

<!--
This should correspond to the last entry on `helm history workshop`
-->
これは `helm history workshop` の最後のエントリーと同じはずです

```sh
helm history workshop
```

<!--
#### Rollback the failed upgrade
-->
#### 失敗したアップグレードをロールバック

<!--
Now we are going to rollback the application to the previous working release revision.
-->
では、以前動いていたリビジョンにアプリケーションをロールバックします。

<!--
First, list Helm release revisions:
-->
まず、Helmのリリースリビジョンを表示します:

```sh
helm history workshop
```

<!--
Then, rollback to the previous application revision (can rollback to any revision too):
-->
そして、以前のアプリケーションリビジョン(どのリビジョンでも可能)にロールバックします:

<!--
```sh
# rollback to the 1st revision
helm rollback workshop 1
```
-->
```sh
# 1つ目のリビジョンにロールバック
helm rollback workshop 1
```

<!--
Validate `workshop` release status with:
-->
`workshop` のリリース状況を確認:

```sh
helm status workshop
```

<!--
Verify that the error is gone
-->
エラーがなくなったことを確認

```sh
kubectl get pods
```

{{< output >}}
NAME                               READY   STATUS             RESTARTS   AGE
ecsdemo-crystal-844d84cb86-56gpz   1/1     Running            0          23m
ecsdemo-crystal-844d84cb86-5vvcg   1/1     Running            0          23m
ecsdemo-crystal-844d84cb86-d2plf   1/1     Running            0          23m
ecsdemo-frontend-6df6d9bb9-dpcsl   1/1     Running            0          23m
ecsdemo-frontend-6df6d9bb9-lzlwh   1/1     Running            0          23m
ecsdemo-frontend-6df6d9bb9-psg69   1/1     Running            0          23m
ecsdemo-nodejs-6fdf964f5f-6cnzl    1/1     Running            0          23m
ecsdemo-nodejs-6fdf964f5f-fbcjv    1/1     Running            0          23m
ecsdemo-nodejs-6fdf964f5f-v88jn    1/1     Running            0          23m
{{< /output >}}
